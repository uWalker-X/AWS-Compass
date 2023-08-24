Segunda atividade prática do programa de bolsas CompassUOL.

autores: Wesley Sergio do Carmo Soares e Luis Paulo Lopes Gonçalves

- REQUISITOS:
1. instalação e configuração do DOCKER ou CONTAINERD no host EC2;
(Ponto adicional para o trabalho: utilizar a instalação via script de Start Instance user_data.sh)
2. Efetuar Deploy de uma aplicação Wordpress com: container de aplicação RDS database Mysql;
3. configuração da utilização do serviço EFS AWS para estáticos do container de aplicação Wordpress;
4. configuração do serviço de Load Balancer AWS para a aplicação Wordpress.

- Pontos de atenção:
  	- Não utilizar ip público para saída do serviços WP (Evitem publicar o serviço WP via IP Público);
  	- Sugestão para o tráfego de internet sair pelo LB (Load Balancer Classic);
	- Pastas públicas e estáticos do wordpress sugestão de utilizar o EFS (Elastic File Sistem);
    - Fica a critério de cada integrante (ou dupla) usar Dockerfile ou Dockercompose;
    - Necessário demonstrar a aplicação wordpress funcionando (tela de login);
    - Aplicação Wordpress precisa estar rodando na porta 80 ou 8080;
    - Utilizar repositório git para versionamento;
    - Criar documentação.

- Daremos início a atividade com o autoscaling, para isso abra a console do EC2 e selecione os serviços de autoscaling
  
OBS: Criaremos um modelo de execução autoscaling com todos os mesmos parâmetros utilizados na intância da primeira atividade juntamente com
os modelos de VPC e security groups da primeira atividade e EFS, nesse contexto, acesse README.md para mais detalhes

primeiramente, utilizaremos o grupo de autoscaling para padronizar e automatizar nossas instâncias

crie um grupo de autoscaling 

- Atente-se às TAGs e configurações de rede, não inclua modelo de execução para sub-rede e habilite a atribuição de IP automática.

 Em 'detalhes avançados' na  user_data, preencha a caixa com os comandos

            #!/bin/bash

            # Atualiza os pacotes e instalar docker
            sudo yum update
            sudo yum install -y docker
            sudo service docker start

            # Configuracoes do EFS
            sudo yum install -y amazon-efs-utils
            sudo mkdir /efs
            # Adicione uma linha com seu comando mount do EFS aqui

            # Outras configuracoes
            curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
            chmod +x /usr/local/bin/docker-compose

OBS: os passos do user_sata é somente para automatizar critérios essenciais de nossas instâncias,
e serão abordados no passo 1, portanto, não é obrigatório.

clique em criar modelo de execução

com o modelo de execução criado

vá para a criação de autoscaling e selecione seu modelo de execução

após isso, selecione sua rede VPC e subnets necessárias (no mínimo duas)
![Captura da Web_23-8-2023_18132_us-east-1 console aws amazon com](https://github.com/uWalker-X/AWS-Compass/assets/138799292/77757ac3-5623-4a8a-a470-0b468c818c6a)

anexe um novo balanceador de carga do tipo application Load Balancer

lembre-se de criar um grupo de destino
![Captura da Web_23-8-2023_184848_us-east-1 console aws amazon com](https://github.com/uWalker-X/AWS-Compass/assets/138799292/4f4bb954-c778-433d-a3c3-c63b63672a64)

na próxima página, selecione a capacidade desejada e as políticas de escalabilidade

avance para a próxima página e se for útil, ative a notificação SNS

em seguida, adicione as TAGs (opicional)

por fim, revise e execute a instância à partir do modelo de execução

conecte-se a instância via SSH ou console AWS


- pronto, configuramos o load balancer pra utilizarmos posteriormente no wordpress


(se você utilizou o user_data para iniciar a instância, ignore este passo)

1. instalção do DOCKER ou CONTAINERD no host EC2
- para a instalação do docker é necessário executar os seguintes comandos:

		sudo yum install -y docker

- em alguns sistemas operacionais o docker é ativado por padrão, para verificar se ele está ativo, digite:

		sudo service docker status
  
caso você obtenha está resosta, será necessário ativa-lo
![docker status](https://github.com/uWalker-X/AWS-Compass/assets/138799292/dfc4c5e8-4185-46cb-83cf-15b757224637)

para ativar basta executar o comando

		sudo service docker Start
para a instalação e ativação do docker compose

        curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
        chmod +x /usr/local/bin/docker-compose
		
2. com o docker-compose ativo, daremos início ao segunto tópico do trabalho: efetuar o deploy do wordpress com container de aplicação RDS database
    pra isso, abra a console AWS, vá ao serviço de RDS e crie um banco de dados
	
o tipo de mecanismo escolhido para o trabalho foi MySQL com a versão mais recente, utilizando o modelo free tier
configure o nome do database, e suas credenciais
    
a classe da instância sugerida e selecionada foi db.t3.micro

	2 vCPUs   -   1GiB RAM   -   Rede:2.085 Mbps

tipo de armazenamento
        
	SSD de uso geral (gp2) - amazenamento alocado de 20 GiB com escalabilidade ativada para até 22 GiB

em conectividade, não se conecte a um recurso de computação EC2

selecione sua VPC criada e o grupo de subnet da sua instância
- é importante frisar um dos pontos de atenção: não utilizar ip público para saída do serviço WP.
  Portanto, não ative o acesso público

  (somente instâncias EC2 e outros recursos dentro da VPC podem se conectar ao banco de dados)

selecione a zona de disponibilidade da sua instância EC2 e mantenha a porta padrão do banco de dados 3306

mantenha a autenticação de senha do banco de dados

  - daremos continuidade ao deploy do wrodpress conectado à instância EC2, após a conexão
    vá ou crie um diretório de sua escolha pra executar o wordpress

execute com um editor de texto o docker-compose, para o ambiente, foi utilizado o VIM



		vim docker-compose.yml

dentro do arquivo de texto

        version: '3.7'
        services:
            wordpress:
                image: wordpress:latest
            volumes:   # o requisito 3 requer utilização do serviço EFS para estáticos da aplicação, entâo selecione odiretório do seu EFS
            - /mnt/efs/website:/var/www/html
            ports:
            - "80:80"
        restart: always
        environment:
            WORDPRESS_DB_HOST: seu DB host (AWS RDS Endpoint)
            WORDPRESS_DB_USER: seu DB user
            WORDPRESS_DB_PASSWORD: sua senha DB 
            WORDPRESS_DB_NAME: nome do seu DB
            WORDPRESS_TABLE_PREFIX: wp_

saia do modo de edição com a tecla 'esc', salve o arquivo com ':wq' para escrever e salvar

com seu docker-compose pronto, execute o comando e espere alguns segundos
        
		sudo docker-compose up -d
para confirmar se o wordpress está funcionando, abra o ip público da intância no navegador

4. para a criação do Load Balancer, abra os serviços EC2 e selecione a aba loadbalancer
- ao criar o load balancer, selecione o tipo application load balancer

nomeie seu Load Balancer

em esquema marque voltado para internet

tipo de endereço ip: IPv4

selecione sua VPC e posteriormente sua security group

- na aba listeners de roteamento, clique no atalho de criar grupo de destino
  ![Captura da Web_24-8-2023_102539_us-east-1 console aws amazon com](https://github.com/uWalker-X/AWS-Compass/assets/138799292/b4cfdd94-0867-4d0b-9105-d7ccb31c3dd8)

  em tipo de destino, selecione instância

  dê um nome ao grupo de destino

  em configurações de protocolos, selecione a porta 80 HTTP

  selecione sua VPC e marque a versão de protocolo HTTP1

  passe para a próxima página e crie seu grupo de destino

de volta a criação do load balancer, selecione o grupo de destino criado

e crie seu load balancer
