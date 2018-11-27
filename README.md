# Codeigniter-Login-and-Email
### Register with hash
```php
public function user_registration()
{
    $firstname = $this->input->post('full_name');
    $last_name = $this->input->post('last_name');
    $email =  $this->input->post('useremail');
    $password =  $this->input->post('password');
    $confrim_password  = $this->input->post('confirmpassword');
    $hash_password = password_hash($password,   PASSWORD_DEFAULT);
    $hash_confirm_password = password_hash($confrim_password, PASSWORD_DEFAULT);

    $user_regist_array = array(
        'firstname'       => $firstname,
        'lastname'        => $last_name,
        'emailid'         => $email,
        'password'        => $hash_password,
        'user_type'       => 2,
        'confirmpassword' => $hash_confirm_password,
        'useraccount_type'=> 0,
        'created_date'    => date('Y-m-d')
    );
    $data = $this->User_model->user_registration($user_regist_array);
    if($data)
    {
        $user_data = $this->User_model->get_userdata($email);
        $this->load->library('email');
        $this->load->library('encrypt');
        $this->email->set_mailtype("html");
        $this->email->from('info@verime.pk');
        $this->email->to($email);
        $this->email->subject('Email Verification');
        $verify = '<a href="verime.pk/profile/activate_account/'.$user_data->verifyToken.'"> here</a>';
        $this->email->message('Hello '.$user_data->firstname.', Congratulations on signing up with Verime. After verification, the next step for you is to set up your profile and start maintaining a verified profile. Verify using this link '.$verify.' or if you cannot open this link, try copying and pasting this URL in your browser: http://www.verime.pk/profile/activate_account/'.$user_data->verifyToken.' Best wishes,
            Team Verime'
        );

        //$this->email->message($verify);
        $this->email->send();
    }

    echo json_encode($data);
}

    
 ```
 ### Login
 ```php
 public function login($login_array)
{
    $condition = array(
        'emailid' => $login_array['emailid'],
        'status' => '1'
    );
    $this->db->select('password')
        ->from('tblusers')
        ->where($condition);
    $hash = $this->db->get()->row();
    if($hash)
    {
        if (password_verify($login_array['password'], $hash->password))
        {
            $this->db->select('*')
                ->from('tblusers')
                ->where('emailid', $login_array['emailid']);
            $query = $this->db->get();
            return $query->row();
        }
    }
    else
    {
       return false;
    }
}
 
 ```
