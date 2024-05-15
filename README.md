<script type="text/javascript">
$(document).ready(function() {
    function processPage(page) {
        $.ajax({
            url: 'index.php?route=catalog/product/bulk&user_token={{ user_token }}&page=' + page,
            type: 'GET',
            success: function(response) {
                console.log('Processed page ' + page + ':', response);
                // Process the next page
                if (response.next_page) {
                    processPage(response.next_page);
                }
            },
            error: function(xhr, status, error) {
                console.error('Error processing page ' + page + ':', error);
            }
        });
    }

    // Start processing from the first page
    processPage(1);
});
</script>
public function bulk() {
    $this->load->language('catalog/product');
    $this->load->model('catalog/product');
    $this->load->model('extension/module/search_by_image');

    $chromaDB = ChromaDB::client();
    $page = isset($this->request->get['page']) ? (int)$this->request->get['page'] : 1;
    $limit = 20; // Adjust the limit as per your pagination settings
    $start = ($page - 1) * $limit;

    $product_total = $this->model_catalog_product->getTotalProducts();
    $products = $this->model_catalog_product->getProducts(['start' => $start, 'limit' => $limit]);

    $images = $this->model_extension_module_search_by_image->getProductImagesByProducts($products);
    
    $ids = [];
    $embeddings = [];
    foreach ($images as $image) {
        $image_path = DIR_IMAGE . $image['image'];
        if (file_exists($image_path)) {
            $image_url = HTTP_SERVER . 'image/' . $image['image'];
            $image_data = base64_encode(file_get_contents($image_path));
            $result = $this->generate($image_data);
            $ids[] = $image['id'];
            $embeddings[] = $result;
        } else {
            echo "not found";
        }
    }

    if (!empty($ids) && !empty($embeddings)) {
        $collection = $chromaDB->getOrCreateCollection('all_products_images_collection');
        $collection->add($ids, $embeddings);
    }

    $response = [
        'status' => 'success',
        'next_page' => $start + $limit < $product_total ? $page + 1 : null
    ];

    $this->response->addHeader('Content-Type: application/json');
    $this->response->setOutput(json_encode($response));
}

public function generate($image_data): array {
    $curl = curl_init();
    curl_setopt_array($curl, array(
        CURLOPT_URL => 'http://192.168.15.253:5000/image/create_embeddings',
        CURLOPT_RETURNTRANSFER => true,
        CURLOPT_ENCODING => '',
        CURLOPT_MAXREDIRS => 10,
        CURLOPT_TIMEOUT => 0,
        CURLOPT_FOLLOWLOCATION => true,
        CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
        CURLOPT_CUSTOMREQUEST => 'POST',
        CURLOPT_POSTFIELDS => json_encode(array("image" => $image_data)),
        CURLOPT_HTTPHEADER => array(
            'Content-Type: application/json'
        ),
    ));
    $response = curl_exec($curl);
    curl_close($curl);
    $embeddings = json_decode($response, true)['embeddings'] ?? [];
    return $embeddings;
}
public function getProductImagesByProducts($products) {
    $images = [];
    foreach ($products as $product) {
        $product_images = $this->getProductImages($product['product_id']);
        $images = array_merge($images, $product_images);
    }
    return $images;
}
