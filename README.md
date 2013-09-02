JVS3Upload - JV S3 Upload
================
Create By: Jaime Marcelo Valasek

Modulo para fazer upload para o seu bucket na Amazon S3.

Esse modulo tem o seu objetivo de uso somente para o upload de arquivos no AWS S3.

Futuros video aulas poderam ser desenvolvidas e postadas no site http://www.zf2.com.br/tutoriais ou no canal do Youtube http://www.youtube.com/zf2tutoriais

Instalação
-----
Faça o download deste modulo para dentro de sua pasta vendor.

Após feito feito os passos acima, abra o arquivo config/application.config.php. E adicione o modulo com o nome JVS3Upload.

Configurando as credenciais
-----
 - Crie um arquivo dentro da pasta global config/autoload/aws.local.php e adicione o código abaixo
 
```php
<?php

return array(
     'Aws' => array(
         'key'    => 'KEY',
         'secret' => 'SECRET_KEY'
     )
);

```

Fazendo o upload do arquivo
-----
Neste exemplo usamos upload de imagem, mas pode facilmente ser qualquer tipo de arquivo.

```php
// pegando a imagem para fazer upload
$fileInfo = $file->getFileInfo();

// nome do arquivo exemplo usando o filtro jvbase_filter_token do JVBase
$nomeDoArquivo = $this->getServiceLocator()->get('jvbase_filter_token')->microtimeToken() . "_" . $fileInfo['thumb_produto']['name'];
		    
// Setando o destino do arquivo dentro do bucket
$nameDestination = "contents/upload/imagem/{$nomeDoArquivo}";

// setando o nome do bucket
$bucket = 'nomedobucket';
		    
// Pegando as configurações do Service Locator
$config = $this->getServiceLocator()->get('config');
		    
// Enviando a imagem para o s3
$s3 = new S3($config["Aws"]["key"], $config["Aws"]["secret"]);
$s3->putBucket($bucket, S3::ACL_PUBLIC_READ);
		    
if($s3->putObjectFile($fileInfo['imagem']['tmp_name'], $bucket , $nameDestination, S3::ACL_PUBLIC_READ) )
{
	// Se chegar aqui é porque o upload foi um sucesso
} 
else 
{
	// Se chegar aqui é porque ocorreu um erro ao fazer o upload
}
```

Fazendo o upload de uma imagem com thumb
-----
```php
// pegando a imagem para fazer upload
$fileInfo = $file->getFileInfo();

// nome do arquivo exemplo usando o filtro jvbase_filter_token do JVBase
$nomeDoArquivo = $this->getServiceLocator()->get('jvbase_filter_token')->microtimeToken() . "_" . $fileInfo['thumb_produto']['name'];
		    
// Setando o destino do arquivo dentro do bucket
$nameDestination = "contents/upload/imagem/{$nomeDoArquivo}";
$nameDestinationThumb = "contents/upload/imagem/thumb/{$nomeDoArquivo}";

// setando o nome do bucket
$bucket = 'nomedobucket';
		    
// Pegando as configurações do Service Locator
$config = $this->getServiceLocator()->get('config');
		    
/* 
 * gerando a miniatura e enviando para o S3
 */
$s3Thumb = new S3Thumb($config, $bucket);
$s3Thumb->putThumb($fileInfo['imagem'], $nameDestinationThumb, 'public-read');
		    
// Enviando a imagem para o s3
$s3 = new S3($config["Aws"]["key"], $config["Aws"]["secret"]);
$s3->putBucket($bucket, S3::ACL_PUBLIC_READ);
		    
if($s3->putObjectFile($fileInfo['imagem']['tmp_name'], $bucket , $nameDestination, S3::ACL_PUBLIC_READ) )
{
	// Se chegar aqui é porque o upload foi um sucesso
} 
else 
{
	// Se chegar aqui é porque ocorreu um erro ao fazer o upload
}
```

Deletando um arquivo no AWS S3
-----
```php
// pegando a imagem para fazer upload
$fileInfo = $file->getFileInfo();

// nome do arquivo exemplo usando o filtro jvbase_filter_token do JVBase
$nomeDoArquivo = $this->params('arquivo');
		    
// Setando o destino do arquivo dentro do bucket
$deleteDestination = "contents/upload/imagem/{$nomeDoArquivo}";

// setando o nome do bucket
$bucket = 'nomedobucket';
		    
// Pegando as configurações do Service Locator
$config = $this->getServiceLocator()->get('config');
		    
// Instanciando
$s3 = new S3($config["Aws"]["key"], $config["Aws"]["secret"]);
$s3->putBucket($bucket, S3::ACL_PUBLIC_READ);
		    
if($s3->deleteObject($bucket, $deleteDestination))
{
	// Se chegar aqui é porque o arquivo foi excluido
} 
else 
{
	// Se chegar aqui é porque o arquivo não foi excluido
}
```