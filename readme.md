  # PROJETO WORDPRESS (COMPASS-UOL)
  ##### Por Karl Richard
  ## Introdução

Neste documento veremos o processo de deploy de um Wordpress com contêiner de aplicação Docker através de uma instância EC2, com banco de dados MySQL no RDS e estáticos do contêiner da aplicação no EFS, ambos na AWS. Juntamente com a configuração de downrecover e alta disponibilidade dos serviços no Auto Scaling Group e Classic Load balancer providos também pela AWS.

## Criando database no AWS RDS

Comecei primeiro criando um database no RDS para armazenar os dados do Wordpress, selecionando do tipo MySQL, nomeando como dpwpsrv e definindo o usuário e senha para acesso ao banco. Nessa ocasião, foi necessário selecionar o tipo de RDS sendo db.t3.micro por conta do free tier, entretanto, pode-se selecionar o tipo que melhor atender a demanda necessária.
Por fim, foi selecionado o DB subnet group que criei antecipadamente com as subnets em diferentes AZs, sem acesso público, a VPC e o security group.

Abaixo imagem do DB criado:

 ![image](https://github.com/user-attachments/assets/84749ec7-73fc-47b9-b8e9-df5c7ab944dd)

## Configurando instância EC2 e instalando Docker

Para fazer o deploy do Wordpress, vamos precisar do docker, e para esse projeto, é requisitado que ele seja instalado em uma EC2. E como no último projeto, subi a EC2 com as mesmas configurações usadas anteriormente, porém com dois detalhes diferentes dessa vez, com o user data com um script de inicialização e com o sistema operacional sendo Debian.

Abaixo imagem da instância criada:

![image](https://github.com/user-attachments/assets/75f0a5c6-524c-4e59-8b84-a8f964343959)

Como podemos ver, sem o IP público, em uma subnet privada (sem rota para um Internet Gateway) e com uma IAM Role fixada, que já vamos detalhar sobre ela.

A principal diferença nessa instância está mais abaixo, nas configurações avançadas, especificamente no user data.
O script user data foi um requisito opcional para o projeto, entretanto ressalto a importância que essa função tem em um ambiente de produção em relação a alta disponibilidade e SLAs de serviços. Por esse motivo criei, com auxilio de IA, um script o mais completo possível, onde possa ser executado uma instância nova sem a necessidade de manipulação de usuário. Ressalto também que há outras formas de executar e criar um ambiente resiliente, entretando optei pelo próprio user data.

Devido a extensão do script, não vou deixar ele em cópia nesse documento, porém vou detalhar brevemente sobre o que ele faz.

Em poucas palavras, ao iniciar a instância, o script faz uma atualização completa, no comando seguinte ele instala o Docker e Docker compose, declarando alguns repositórios:

![image](https://github.com/user-attachments/assets/2c0b0094-5e7d-4923-bc67-ed6c840c531a)


