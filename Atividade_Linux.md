> Requisitos AWS:

	¬ Gerar uma chave pública para acesso ao ambiente;
 	¬ Criar uma instância EC2 com o sistema operacional Amazon Linux 2 (Família t3 small, 16GB SSD);
 	¬ Gerar um elastci IP e anexar à instância EC2;
 	¬ Liberar portas de comunicação para acesso público: (22/TCP, 111/TCP/UDP, 2049/TCP/UDP, 80/TCP, 443/TCP).
  
> Requisitos Linux:

	¬ configurar NFS entregue;
	¬ criar um diretório dentro do filesystemdo NFS com o seu nome;
	¬ criar um script que valide se o serviço esta online e envie o resultado da validação para o NFS;
	¬ o scrit deve gerar 2 arquivos de saída: 1 para o serviço online e um para o serviço offline;
	¬ preparar a execução automatizada do script a cada 5 minutos.
  
Para a conclusão deste trabalho, foi necessário a utilização e conhecimento da Amazon Web Services, além de conhecimento em lógica de programação e Linux
# criando uma VPC

Na console AWS, Selecione os serviços VPC e crie uma nova VPC

marque a caixa VPC e muito mais e gerar automaticamente a etiqueta do nome
  ![Captura da Web_24-8-2023_112120_us-east-1 console aws amazon com](https://github.com/uWalker-X/AWS-Compass/assets/138799292/780fd0bb-d117-4ac0-a1bb-1f8b4dbe03b3)
  	crie no mínimo duas zonas de disponibilidade

  	com duas subnets privadas e duas subnets públicas
	Gateway NAT e Endpoints da VPC não são necessárias para essa atividade, portanto, são opicionais

- No conole AWS, seleione os serviços EC2 e configure security group referente a VPC criada.

libere as seguintes portas:
  
		TCP: 80 > 0.0.0.0
  		TCP: 22 > 0.0.0.0
  		TCP: 443 > 0.0.0.0
  		TCP: 3306 > 0.0.0.0
 		TCP/UDP: 111 > 0.0.0.0
  		TCP/UDP: 2049 > 0.0.0.0

# criar EFS, e associa-la a VPC criada
- no console AWS, selecione os serviços EFS e crie um sistema de arquivos

- selecione sua VPC e nomeie seu EFS

- ao criar, clicando no nome ou ID do EFS será mostrado os detalhes do EFS

- clicando em anexar será exibido o comando "mount" do seu EFS que será usado posteriormente

# criando uma instância EC2
- crie uma instância EC2 com sistema operacional Amazon Linux 2
nos serviços EC2, na aba instância do console AWS

vamos executar nossa primeira instância

- o padrão Compass exige o uso de TAGs específicas para a criação de instâncias e volumes
![Captura da Web_24-8-2023_11344_us-east-1 console aws amazon com](https://github.com/uWalker-X/AWS-Compass/assets/138799292/044e7257-c92d-4822-b393-7f8281b4d97e)

		name: PB IFMT - UTFPR - UNAMA
  		CostCenter: C092000004
  		Project: PB IFMT - UTFPR - UNAMA

- imagens de aplicação e de sistema operacional
usaremos Amazon Linux - 64 bits (x86)

tipo de instância t2.micro ou t3.micro

- configurações de rede
edite e selecione sua VPC

marque uma subnet pública

> e em firewall, escolha seu security group configurado

- OBS: habilite o IPv4 público para ter aceso à instância

- par de chaves
	clique no atalho de criar novo par de chaves

	insira um nome e selecione o tipo de chave RSA no formato .pem

  		faça download do seu par de chaves, pois será usado posteriormente

- volume do EBS
	mantenha as configurações, porém, em sistemas de arquivos

 	marque a caixa EFS e clique em adicionar sistema compartilhado

  	seu EFS montará de fora automática assim que a instância iniciar

- execute a instância
  
- conecte-se a sua instância EC2 criada
> você pode se conectar à instância pela console AWS, mas para essa atividade é exigida uma chave pública para acesso remoto, então usaremos a CLI para se conectar usando o par de chaves RSA criado anteriormente. Portanto,

> localize o diretório do seu par de chaves na sua máquina

> na console AWS, clique em conectar-se em sua instancia na aba SSH, será exibido um comand line para a conexão remota

> copie esse comando e adicione o diretório em que se localiza o seu par de chaves, como no exemplo abaixo

	aws ec2-instance-connect ssh --instance-id i-1234567890example --private-key-file /path/to/key_example.pem
# conectado à instância
	
- execute o comando df -T -h
  se exibir o ID do seu EFS montado, signifca que ocorreu de forma automática e com exito

  caso contrário, dirija-se ao serviço EFS na console AWS e copie o comando que aparece na aba anexar

- crie um diretório com seu nome na pasta do EFS

  		mkdir seu_nome

- para subir e configurar o apache foi usado o comando

			sudo yum install -y httpd
			sudo systemctl enable httpd
			sudo systemct1 status httpd
			sudo systemctl start httpd

- use o ip público para acessar

- a imagem mostra o servidor online, sem alterações até então
