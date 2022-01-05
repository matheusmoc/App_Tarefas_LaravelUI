<strong>CONFIGURAÇÕES INICIAIS</strong>

composer require laravel/ui

php artisan ui bootstrap --auth
php artisan ui vue --auth
php artisan ui react --auth

npm run dev 

//caso dê erro intalar o npm ou atualizar o npm

npm install

php artisan list   //procurar por comandos ui 


-------------------------------------------------------###-------------------------------------------------------------

<strong>CONFIGURAÇÃO DO SERVIDOR SMTP</strong>

MAIL_MAILER=smtp <br>
MAIL_HOST=smtp.gmail.com <br>
MAIL_PORT=587<br>
MAIL_USERNAME=tarefas23@gmail.com<br>
MAIL_PASSWORD=***********<br>
MAIL_ENCRYPTION=TLS<br>
MAIL_FROM_ADDRESS="${MAIL_USERNAME}"<br>
MAIL_FROM_NAME="${APP_NAME}"<br>

-------------------------------------------------------###-------------------------------------------------------------



<strong>NOTIFICAÇÃO DE RESET DE SENHA COM TEMPLATE PERSONALIZADO</strong>

//php artisan make:mail MensagemMail --markdown emails.mensagem

app\Mail\MensagemMail.php

// php artisan make:notification RedefinirSenhaNotification

app\Notifications\RedefinirSenhaNotification.php

sobreescrever o método padrão do laravel para envio de emails para reset de senha, intercepta o fluxo


app\Models\User

<pre>
public function sendPasswordResetNotification($token) { 
    //dd('chegamos até aqui');
    $this->notify(new RedefinirSenhaNotification($token, $this->email,  $this->name)); //definir qual o objeto de notificação vai ser utilizado
}

</pre>

-------------------------------------------------------###-------------------------------------------------------------


<strong>DIRETORIO DA VIEW DE TEMPLATE DE EMAIL</strong>

vendor\laravel\framework\src\Illuminate\Notifications\resources\views\email.blade.php

-------------------------------------------------------###-------------------------------------------------------------

<strong>DIRETORIO DAS VALIDAÇÕES DO RESET DE SENHA</strong>

vendor\laravel\ui\auth-backend\ResetsPasswords.php


    protected function rules()
    {
        return [
            'token' => 'required',
            'email' => 'required|email',
            'password' => 'required|confirmed|min:8',
        ];
    }


-------------------------------------------------------###-------------------------------------------------------------


<strong>HABILITAR SEGURANÇA DE EMAIL</strong>

MustVerifyEmail (evitar cadastros de emails falsos ou de terceiros)


Models >>> User  

//implementar na classe o MustVerifyEmail

class User extends Authenticatable implements MustVerifyEmail

Route >>> web 

// Indicando a validação de email na rota de autenticação

Auth::routes(['verify' => true]);

//adicionar middlera nas rotas para a autenticação ocorrer somente depois que o usuário for verificado

->middleware('verified');


-------------------------------------------------------###-------------------------------------------------------------

<strong>VIEW DE VERIFICAÇÃO DE EMAIL</strong>

resources\views\auth\verify.blade.php

-------------------------------------------------------###-------------------------------------------------------------

<strong>CUSTOMIZAR MENSAGEM DE VERIFICAÇÃO DE EMAILS</strong>

 // php artisan make:notification VerificarEmailNotification

 Models >>> User  
 
  <pre>

     public function sendEmailVerificationNotification(){ //fluxo de verificação de email ao qual intercepta o usuário e desvio de fluxo
        $this->notfy(new VerificarEmailNotification);
    }
	
	
  </pre>



copiar toda logica do diretorio vendor abaixo e colocar em verification/VerificarEmailNotification

vendor\laravel\framework\src\Illuminate\Auth\Notifications\VerifyEmail.php


<h4>ALTERAÇÕES:</h4>

//namespace Illuminate\Auth\Notifications;<br>
//namespace App\Notifications;<br>


//class VerifyEmail extends Notification <br>
//class VerificarEmailNotification extends Notification <br>

alterar mensagem 

 <pre>
 protected function buildMailMessage($url)
    {
        return (new MailMessage)
            ->subject('Comfirmação de email')
            ->greenting('Olá'.$this->name)
            ->line('Clique no botão abaixo para validar seu email')
            ->action('Clique aqui para validar seu email', $url)
            ->line('Caso não seja cadastrado, deconsidere a mensagem');
    }

</pre>

passsar nome do usuario no fluxo de verificação 


Model >>> user 


<pre>
public function sendEmailVerificationNotification(){ //fluxo de verificação de email ao qual intercepta o usuário e desvio de fluxo
        $this->notfy(new VerificarEmailNotification($this->name));
    }

}
</pre>

criar o construtor para passar o nome do usuario

 
<pre>
public function __construct($name){
        $this->name = $name;
}

</pre>
-------------------------------------------------------###-------------------------------------------------------------
