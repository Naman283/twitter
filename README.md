



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
