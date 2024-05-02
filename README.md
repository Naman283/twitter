
// Controller

<?php
class ControllerExtensionModuleBgRemove extends Controller {
    public function index() {
        $this->load->language('extension/module/bgremove');
    
        $this->document->setTitle($this->language->get('heading_title'));
    
        $this->load->model('tool/image');
    
        $error = array();
    
        if (($this->request->server['REQUEST_METHOD'] == 'POST') && $this->validate()) {
            $image_data = '';
    
            if (!empty($this->request->files['image']['tmp_name']) && is_uploaded_file($this->request->files['image']['tmp_name'])) {

                $image_data = file_get_contents($this->request->files['image']['tmp_name']);

                $image_data = base64_encode($image_data);
            }
    
            if (!empty($image_data)) {
                $result = $this->removeBackground($image_data);
                if ($result['success']) {
                    $this->session->data['success'] = $result['message'];
                    $data['modified_image'] = $result['modified_image'];
                } else {
                    $error['warning'] = $result['error'];
                }
            } else {
                $error['warning'] = 'Please upload an image.';
            }
        }
    
        $data['heading_title'] = $this->language->get('heading_title');
        $data['error_warning'] = isset($error['warning']) ? $error['warning'] : '';
    
        return $this->load->view('extension/module/bgremove', $data);
    }
    

    private function validate() {
        
        return true;
    }

    private function removeBackground($image_data) {
        $api_key = 'coJFngPJ381j25P7K4XK87G5';
        $url = 'https://api.remove.bg/v1.0/removebg';
        $headers = array(
            'X-Api-Key: ' . $api_key
        );
        $data = array(
            'image_file_b64' => $image_data,
            'size' => 'regular'
        );
    
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
        curl_setopt($ch, CURLOPT_POST, 1);
        curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query($data));
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    
        $response = curl_exec($ch);
        $http_status = curl_getinfo($ch, CURLINFO_HTTP_CODE);
    
        curl_close($ch);
    
        // Process API response
        if ($http_status == 200) {
            // Successful response
            return array(
                'success' => true,
                'message' => 'Background removed successfully.',
                'modified_image' => 'data:image/png;base64,' . base64_encode($response)
            );
        } else {
            // Error response
            return array(
                'success' => false,
                'error' => 'Failed to remove background. HTTP Status Code: ' . $http_status
            );
        }
    }
    
}


// Twig File

<div class="bg-remove">
    {% if error_warning %}
        <div class="alert alert-danger">{{ error_warning }}</div>
    {% endif %}
    <form action="{{ action }}" method="post" enctype="multipart/form-data">
        <div class="form-group">
            <label for="image">Upload Image:</label>
            <input type="file" name="image" id="image" class="form-control">
        </div>
        <button type="submit" class="btn btn-primary">Remove Background</button>
    </form>
    {% if modified_image %}
        <h2>Modified Image:</h2>
        <img src="{{ modified_image }}" alt="Modified Image">
    {% endif %}
</div>

// language 

<?php
// Heading
$_['heading_title'] = 'Background Removal';



// AJAX  

<!-- catalog/view/theme/default/template/extension/module/bgremove.twig -->
<div id="bgremove">
    <h2>{{ heading_title }}</h2>
    
    {% if error_warning %}
    <div class="alert alert-danger">{{ error_warning }}</div>
    {% endif %}
    
    <form id="upload-form" enctype="multipart/form-data">
        <div class="form-group">
            <label for="image">Select Image:</label>
            <input type="file" name="image" id="image" class="form-control" required>
        </div>
        <button type="submit" class="btn btn-primary">Remove Background</button>
    </form>
    
    <div id="result"></div>
</div>

<script>
    $(document).ready(function() {
        $('#upload-form').submit(function(e) {
            e.preventDefault();

            var formData = new FormData($(this)[0]);

            $.ajax({
                url: 'index.php?route=extension/module/bgremove',
                type: 'POST',
                data: formData,
                processData: false,
                contentType: false,
                success: function(response) {
                    response = JSON.parse(response);
                    if (response.success) {
                        $('#result').html('<img src="' + response.modified_image + '">');
                    } else {
                        alert('Error: ' + response.error);
                    }
                },
                error: function() {
                    alert('An error occurred while processing your request.');
                }
            });
        });
    });
</script>




//  controller

<?php
// Controller file: catalog/controller/extension/module/bgremove.php
class ControllerExtensionModuleBgRemove extends Controller {
    public function index() {
        $this->load->language('extension/module/bgremove');

        $this->document->setTitle($this->language->get('heading_title'));

        $this->load->model('tool/image');

        $error = array();

        if ($this->request->server['REQUEST_METHOD'] == 'POST') {
            if (!empty($this->request->files['image']['tmp_name']) && is_uploaded_file($this->request->files['image']['tmp_name'])) {
                $image_data = file_get_contents($this->request->files['image']['tmp_name']);
                $image_data = base64_encode($image_data);

                $result = $this->removeBackground($image_data);

                if ($result['success']) {
                    $this->response->setOutput(json_encode(array('success' => true, 'modified_image' => $result['modified_image'])));
                    return;
                } else {
                    $this->response->setOutput(json_encode(array('success' => false, 'error' => $result['error'])));
                    return;
                }
            } else {
                $this->response->setOutput(json_encode(array('success' => false, 'error' => 'Please upload an image.')));
                return;
            }
        }

        $data['heading_title'] = $this->language->get('heading_title');
        $data['error_warning'] = isset($error['warning']) ? $error['warning'] : '';

        $this->response->setOutput($this->load->view('extension/module/bgremove', $data));
    }

    private function removeBackground($image_data) {
        $api_key = 'VVWB468LUnChzQC3vVTJipDg';
        $url = 'https://api.remove.bg/v1.0/removebg';
        $headers = array(
            'X-Api-Key: ' . $api_key
        );
        $data = array(
            'image_file_b64' => $image_data,
            'size' => 'regular'
        );

        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
        curl_setopt($ch, CURLOPT_POST, 1);
        curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query($data));
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);

        $response = curl_exec($ch);
        $http_status = curl_getinfo($ch, CURLINFO_HTTP_CODE);

        curl_close($ch);

        // Process API response
        if ($http_status == 200) {
            // Successful response
            return array(
                'success' => true,
                'modified_image' => 'data:image/png;base64,' . base64_encode($response)
            );
        } else {
            // Error response
            return array(
                'success' => false,
                'error' => 'Failed to remove background. HTTP Status Code: ' . $http_status
            );
        }
    }
}



{{ header }}{{ column_left }}

<div id="content">

  <div class="page-header">

    <div class="container-fluid">

      <div class="pull-right">

        <button type="submit" form="form-module" data-toggle="tooltip" title="{{ button_save }}" class="btn btn-primary"><i class="fa fa-save"></i></button>

        <a href="{{ cancel }}" data-toggle="tooltip" title="{{ button_cancel }}" class="btn btn-default"><i class="fa fa-reply"></i></a></div>

      <h1>{{ heading_title }}</h1>

      <ul class="breadcrumb">

        {% for breadcrumb in breadcrumbs %}

        <li><a href="{{ breadcrumb.href }}">{{ breadcrumb.text }}</a></li>

        {% endfor %}

      </ul>

    </div>

  </div>

  <div class="container-fluid">

    {% if error_warning %}

    <div class="alert alert-danger alert-dismissible"><i class="fa fa-exclamation-circle"></i> {{ error_warning }}

      <button type="button" class="close" data-dismiss="alert">&times;</button>

    </div>

    {% endif %}

    <div class="panel panel-default">

      <div class="panel-heading">

        <h3 class="panel-title"><i class="fa fa-pencil"></i> {{ text_edit }}</h3>

      </div>

      <div class="panel-body">

        <form action="{{ action }}" method="post" enctype="multipart/form-data" id="form-module" class="form-horizontal">







        <div class="form-group">

            <label class="col-sm-2 control-label" for="input-input">{{ entry_input }}</label>

            <div class="col-sm-10">

              <input type="text" name="module_abc_input" value="{{ module_abc_input }}" placeholder="{{ entry_input }}" id="input-input" class="form-control" />

              {% if error_input %}

              <div class="text-danger">{{ error_input }}</div>

              {% endif %}

            </div>

          </div>







          <div class="form-group">

            <label class="col-sm-2 control-label" for="input-status">{{ entry_status }}</label>

            <div class="col-sm-10">

              <select name="module_abc_status" id="input-status" class="form-control">

                {% if module_abc_status %}

                <option value="1" selected="selected">{{ text_enabled }}</option>

                <option value="0">{{ text_disabled }}</option>

                {% else %}

                <option value="1">{{ text_enabled }}</option>

                <option value="0" selected="selected">{{ text_disabled }}</option>

                {% endif %}

              </select>

            </div>

          </div>

        </form>

      </div>

    </div>

  </div>

</div>

{{ footer }}







<?php

// Heading

$_['heading_title']    = 'Abc';



// Text

$_['text_extension']   = 'Extensions';

$_['text_success']     = 'Success: You have modified abc module!';

$_['text_edit']        = 'Edit Abc Module';



// Entry

$_['entry_status']     = 'Status';

$_['entry_input']      = 'Input';









// Error

$_['error_input']      = 'input required!';

$_['error_input_validation']      = 'input must be alphabets!';



$_['error_permission'] = 'Warning: You do not have permission to modify abc module!';







<?xml version="1.0" encoding="utf-8"?>

<modification>

    <name>Menu MOdification</name>

    <version>1</version>

    <author>Naman Chauhan</author>

    <link>http://localhost/opencart/upload/</link>

    <code>modification</code>

    <description>modification</description>

    <file path="admin/controller/common/column_left.php">

        <operation>

            <search><![CDATA[ 			$catalog = array();

 ]]></search>

            <add position="before"><![CDATA[ 

            	if ($this->user->hasPermission('access', 'extension/module/abc')) {

			$data['menus'][] = array(

				'id'       => 'menu-abc',

				'name'	   =>$this->config->get('module_abc_input'),

				'icon'	   => 'fa-cog',

				'href'     => $this->url->link('extension/module/abc', 'user_token=' . $this->session->data['user_token'], true),

				'children' => array()

			);

		}



		$abc = array();



			$abc[] = array(

				'name'	   => $this->language->get('text_abc'),

				'href'     => $this->url->link('common/abc', 'user_token=' . $this->session->data['user_token'], true),

				'children' => array()

			);

			$abc[] = array(

				'name'	   => $this->language->get('text_abc'),

				'href'     => $this->url->link('common/abc', 'user_token=' . $this->session->data['user_token'], true),

				'children' => array()

			);

		

		if ($abc) {

			$data['menus'][] = array(

				'id'       => 'menu-abc',

				'icon'	   => 'fa-cog',



				'name'	   => $this->language->get('text_abc'),

				'href'     => '',

				'children' => $abc

			);

		}



		

            ]]></add>

        </operation>

    </file>

<file path="admin/language/en-gb/common/column_left.php">

        <operation>

            <search><![CDATA[ $_['text_api']                  = 'API';

 ]]></search>

            <add position="before"><![CDATA[

            $_['text_abc']                  = 'Abc';



            ]]></add>

        </operation>

    </file>



</modification>



<?php

class ControllerExtensionModuleAbc extends Controller {



	

	private $error = array();



	public function index() {

		$this->load->language('extension/module/abc');



		$this->document->setTitle($this->language->get('heading_title'));



		$this->load->model('setting/setting');



		if (($this->request->server['REQUEST_METHOD'] == 'POST') && $this->validate()) {

			$this->model_setting_setting->editSetting('module_abc', $this->request->post);



			$this->session->data['success'] = $this->language->get('text_success');



			$this->response->redirect($this->url->link('marketplace/extension', 'user_token=' . $this->session->data['user_token'] . '&type=module', true));

		}



		if (isset($this->error['warning'])) {

			$data['error_warning'] = $this->error['warning'];

		} else {

			$data['error_warning'] = '';

		}



		if (isset($this->error['module_abc_input'])) {

			$data['error_input'] = $this->error['module_abc_input'];

		} else {

			$data['error_input'] = '';

		}





		$data['breadcrumbs'] = array();



		$data['breadcrumbs'][] = array(

			'text' => $this->language->get('text_home'),

			'href' => $this->url->link('common/dashboard', 'user_token=' . $this->session->data['user_token'], true)

		);



		$data['breadcrumbs'][] = array(

			'text' => $this->language->get('text_extension'),

			'href' => $this->url->link('marketplace/extension', 'user_token=' . $this->session->data['user_token'] . '&type=module', true)

		);



		$data['breadcrumbs'][] = array(

			'text' => $this->language->get('heading_title'),

			'href' => $this->url->link('extension/module/abc', 'user_token=' . $this->session->data['user_token'], true)

		);



		$data['action'] = $this->url->link('extension/module/abc', 'user_token=' . $this->session->data['user_token'], true);



		$data['cancel'] = $this->url->link('marketplace/extension', 'user_token=' . $this->session->data['user_token'] . '&type=module', true);



		if (isset($this->request->post['module_abc_status'])) {

			$data['module_abc_status'] = $this->request->post['module_abc_status'];

		} else {

			$data['module_abc_status'] = $this->config->get('module_abc_status');

		}



		

		if (isset($this->request->post['module_abc_input'])) {

			$data['module_abc_input'] = $this->request->post['module_abc_input'];

		} else {

			$data['module_abc_input'] = $this->config->get('module_abc_input');

		}



		$data['header'] = $this->load->controller('common/header');

		$data['column_left'] = $this->load->controller('common/column_left');

		$data['footer'] = $this->load->controller('common/footer');



		$this->response->setOutput($this->load->view('extension/module/abc', $data));

	}



	protected function validate() {

		if (!$this->user->hasPermission('modify', 'extension/module/abc')) {

			$this->error['warning'] = $this->language->get('error_permission');

		}



		if (!preg_match('/^[a-zA-Z]+$/', $this->request->post['module_abc_input'])) {

			$this->error['module_abc_input'] = $this->language->get('error_input_validation');

		}

		



		return !$this->error;

	}

}
