segunda atividade prática do programa de bolsas CompassUOL
1. instalação e configuração do DOCKER ou CONTAINERD no host EC2;
Ponto adicional para o trabalho utilizar a instalação via script de Start Instance (user_data.sh)
2. Efetuar Deploy de uma aplicação Wordpress com: container de aplicação RDS database Mysql
3. configuração da utilização do serviço EFS AWS para estáticos do container de aplicação Wordpress
4. configuração do serviço de Load Balancer AWS para a aplicação Wordpress

Pontos de atenção:
    não utilizar ip público para saída do serviços WP (Evitem publicar o serviço WP via IP Público)
    sugestão para o tráfego de internet sair pelo LB (Load Balancer Classic)
    pastas públicas e estáticos do wordpress sugestão de utilizar o EFS (Elastic File Sistem)
    Fica a critério de cada integrante (ou dupla) usar Dockerfile ou Dockercompose;
    Necessário demonstrar a aplicação wordpress funcionando (tela de login)
    Aplicação Wordpress precisa estar rodando na porta 80 ou 8080;
    Utilizar repositório git para versionamento;
    Criar documentação.

4-
OBS: Criaremos um modelo de execução autoscaling com todos os mesmos parâmetros utilizados na intância da primeira atividade
    portanto, vá aos serviços de EC2, e selecione o serviço autoscaling
         crie um modelo de execução baseado na orientação da primeira atividade
         # Atente-se às configurações de rede, não inclua modelo de execução para sub-rede e habilite a atribuição de IP automática
         em 'detalhes avançados' na  user_data, preencha a caixa com os comandos

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

        OBS: os passos do user_sata é somente para automatizar critérios essenciais de nossas instâncias, e serão abordados no passo 1-, portanto, não é obrigatório
    
        clique em criar modelo de execução
        vá para a criação de autoscaling e selecione seu modelo de execução
            após isso, selecione sua rede VPC e subnets necessárias (no mínimo duas)
            anexe um novo balanceador de carga do tipo application Load Balancer
            na próxima página, selecione a capacidade desejada e as políticas de escalabilidade
            avance para a próxima página e se for útil, ative a notificação SNS
            em seguida, adicione as TAGs
            por fim, revise e execute a instância à partir do modelo de execução
        conecte-se a instância via SSH ou console AWS


(se você utilizou o user_data para iniciar a instância, ignore este passo)
1- instalção do DOCKER ou CONTAINERD no host EC2
    para a instalação e ativação do docker é necessário executar os seguintes comandos:
        sudo yum install -y docker
    em alguns sistemas operacionais o docker é ativado por padrão, para verificar se ele está ativo, digite:
        sudo service docker status
    caso você obtenha está resosta, será necessário ativa-lo
        [ec2-user@ip-10-0-2-10 ~]$ sudo service docker status
        Redirecting to /bin/systemctl status docker.service
        ○ docker.service - Docker Application Container Engine
            Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; preset: disabled)
            Active: inactive (dead)
        TriggeredBy: ○ docker.socket
        Docs: https://docs.docker.com
    para ativar basta executar o comando
        sudo service docker Start
    para a instalação e ativação do docker compose
        curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
        chmod +x /usr/local/bin/docker-compose
2- com o docker-compose ativo, daremos início ao segunto tópico do trabalho: efetuar o deploy do wordpress com container de aplicação RDS database
    pra isso, abra a console AWS, vá ao serviço de RDS e crie um banco de dados
    o tipo de mecanismo escolhido para o trabalho foi MySQL com a versão mais recente, utilizando o modelo free tier
    configure o nome do database, e suas credenciais
    a classe da instância sugerida e selecionada foi db.t3.micro
        2 vCPUs   -   1GiB RAM   -   Rede:2.085 Mbps
    tipo de armazenamento
        SSD de uso geral (gp2) - amazenamento alocado de 20 GiB com escalabilidade ativada para até 22 GiB
    em conectividade, não se conecte a um recurso de computação EC2
        selecione sua VPC criada e o grupo de subnet da sua instância
    é importante frisar um dos pontos de atenção: não utilizar ip público para saída do serviço WP
        portanto, não ative o acesso público (somente instâncias EC2 e outros recursos dentro da VPC podem se cnectar ao banco de dados)
        selecione a zona de disponibilidade da sua instância EC2 e mantenha a porta padrão do banco de dados 3306
        mantenha a autenticação de senha do banco de dados
    daremos contínuidade ao deploy do wrodpress conectado à instância EC2, após a conexão
    vá ou crie um diretório de sua escolha pra executar o wordpress
    execute com um editor de texto o docker-compose, para o ambiente, foi utilizado o VIM
        vim docker-compose.yml
    dentro do arquivo de texto

        version: '3.7'
        services:
            wordpress:
                image: wordpress:latest
            volumes:     #Aloque pastas públicas e e estáticas do wp no diretório do seu EFS
            - /mnt/efs/website:/var/www/html
            ports:
            - "8080:80"
        restart: always
        environment:
            WORDPRESS_DB_HOST: seu DB host (AWS RDS Endpoint)
            WORDPRESS_DB_USER: seu DB user
            WORDPRESS_DB_PASSWORD: sua senha DB 
            WORDPRESS_DB_NAME: nome d eu DB
            WORDPRESS_TABLE_PREFIX: wp_

    saia do modo de edição com a tecla 'esc', salve o arquivo com ':wq' para escrever e salvar
    com seu docker-compose pronto, execute o comando
        sudo docker-compose up -d
    para confirmar se o wordpress está funcionando, abra o ip público da intância no navegador
