Requisitos AWS:
	gerar uma chave pública para acesso ao ambiente;
	criar uma instância EC2 com o sistema operacional Amazon Linux 2 (Família t3 small, 16GB SSD);
	gerar um elastci IP e anexar à instância EC2;
	liberar portas de comunicação para acesso público: (22/TCP, 111/TCP/UDP, 2049/TCP/UDP, 80/TCP, 443/TCP).
Requisitos Linux:
	configurar NFS entregue;
	criar um diretório dentro do filesystemdo NFS com o seu nome;
 	criar um script que valide se o serviço esta online e envie o resultado da validação para o NFS;
  	o scrit deve gerar 2 arquivos de saída: 1 para o serviço online e um para o serviço offline;
   	preparar a execução automatizada do script a cad 5 minutos.
  


- Criar VPC padrão
![vpc](https://github.com/uWalker-X/Atividade-Linux/assets/138799292/49e17f0b-d9b5-46eb-9fc1-5a0a1a24072b)

- configurar security group referente a VPC criada com a seguintes portas liberadas
![Security Group VPC](https://github.com/uWalker-X/Atividade-Linux/assets/138799292/deb826c7-a326-468f-8216-75e41493dd53)

- criar EFS padrão, e associando a VPC padrão criada
![nfs](https://github.com/uWalker-X/Atividade-Linux/assets/138799292/1e4d7e09-3dfd-47ed-9d02-0226fa5719d3)

- crie uma instância EC2 com sistema operacional Amazon Linux 2
![OS EC2](https://github.com/uWalker-X/Atividade-Linux/assets/138799292/d5a79ebd-5e26-4e42-8032-817fd3146e3e)

- o padrão Compass exige o uso de TAGs específicas para instâncias e volumes para a criação
![Tags EC2](https://github.com/uWalker-X/Atividade-Linux/assets/138799292/2bae48d4-fcde-4914-80a0-6ce17f161632)

- em configurações de rede, marque sua VPC e grupo de segurança criados
![conf  de rede](https://github.com/uWalker-X/Atividade-Linux/assets/138799292/7ca7b9e0-c908-4efa-9df1-e199ce41a605)

- habilite também o IPv4 público para ter aceso à instância

- em volume EBS selecione sistema de arquivos e escolha sua NFS criada
![volume EC2](https://github.com/uWalker-X/Atividade-Linux/assets/138799292/65848fbd-6f19-4714-accb-c95e5e3b22a8)

- executar a instância
- conecte-se a sua instância EC2 criada
![primeira conexão](https://github.com/uWalker-X/Atividade-Linux/assets/138799292/e8b34862-f267-4eb3-a4ff-83a464548fba)

- execute o comando df -T -h
![teste NFS](https://github.com/uWalker-X/Atividade-Linux/assets/138799292/4a864571-fdc4-43b5-9a98-c5736320c487)

- foi criado um diretório com meu nome dentro do filesystem do NFS usando mkdir
![nome do filesysten NFS](https://github.com/uWalker-X/Atividade-Linux/assets/138799292/d0c394de-352d-41ca-a80a-ee5650c3c42d)

- para subir e configurar o apache foi usado o comando

			sudo yum install -y httpd
			sudo systemctl enable httpd
			sudo systemct1 status httpd
			sudo systemctl start httpd

- use o ip público para acessar

![apache online](https://github.com/uWalker-X/Atividade-Linux/assets/138799292/e48c05c8-584f-439b-b06b-a12a45d85116)

- a imagem mostra o servidor online, sem alterações até então
- após o comando: sudo echo '<center><h1>este servidor web apache está roando em uma intância EC2 da AWS </h1></center>' > /var/www/html/index.html
-  o site passou a se apresentar desta forma
![pós alterações](https://github.com/uWalker-X/Atividade-Linux/assets/138799292/c1fc3879-c910-43b6-8e92-09fcddfa49d3)
