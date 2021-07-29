# desafiodiodataengdio
respositório do desafio final do Bootcamp Data Engineer carrefour - Digital Innovation one
#Reginaldo Yuji Katayose 
#Roteiro do desafio.


Criando um Ecossistema Hadoop Totalmente Gerenciado com Google Cloud Dataproc

1- 	Criar Projeto


2- 	A Interface de Faturamento
	Criação de Alerta de Orçamento (Budget Notification)
	Dentro de Faturameto clique na coluna da esquerda "Orçamento e alertas"
	em "Criar Orçamento"
	Preencha o Escopo com:
		-Nome:<<Nome do seu Projeto>>
		Serviços: <<Selecione Serviços específicos>> ou Todos (é nosso caso) 
		1689 serviços 
		
		-Clique em não colocar  na contabilização os creditos utilizados
		Flegar  
		[x] Descontos
		[x] Promoções e outros creditos
		
		Clique em <<proximo>>
		
		Agora estaremos Setando o Valor:
			Selecione Tipo de Orçamento, aqui podemos colocar um valor ou 
			podemos colocar um gasto do mês anterior como referência.
			Porem ao utilizar o gasto de um determinado periodo qualquer variância
			para cima ou para baixo vai gerar alerta tenha conciência disso.
			
			-vamos setar 100 reais e clicar em <<Proxima>>
			
			-Agora vamos setar os alertas as ações para cada porcentagem do orçamento
			ex 50% 90% 100%, obs isso não bloqueia o projeto quando o valor é alcançado
			só notifica.
			Vamos configurar 50% 75% 100%
			
			opções de notificação:
			
			1- Enviar alertas por e-mail. aos administradores e usuarios de faturamento
				(Mais utilizado)
			
			[ ]	Vincula  os canais de notificação por e-mail do Monitoring a este orçamento
			[ ] Conectar um topico do pub/sub a este orçamento
				Utilizado para tomar ações(Cloud Function) após condição ser satisfeita.
				Fluxo:
				-Alerta é atingido
				-pub/sub chama a função
				-a função criada em python ou Javascript qq outra linguagem pode cortar 
				o faturamento (script nao é desenvolvido pelo Google vc mesmo tem q desenvolver)
			
			para finalizar clique em <<Conluir>>
			
		

3-	Demonstração de Compute Engine
Entendendo Computer Engine (video) 





4-	Habilitando API´s de serviços
	1- Clique no menu da esquerda procure por 
	Compute Engine>>> Instancias de VM >> Habilite a API
	2- Clicar em Criar instancia
		Serviço gerenciado e Não gerenciado: neste caso estamos fazendo 
		manualmente o que é necessario.
	3- Na tela de Criar instancia preencha o NOME: coloquei Instancia-hadoop	
	Escolha  a Região e Zona a ser criado o Servidor eu escolhi Southamerica-east1(São Paulo) 
	e Zona southamerica-east1-a
	Selecione a maquina eu deixei default e2-medium(2 vCPU,4GB de memoria)
	Configure Sistema
	no meu caso escolhi linux Ubuntu 20.04 LTS
	Tamanho de disco 100GB o restante nao mexemos clicar em <<Selecionar>>
	
	Identidade e acesso a API
		Conta de serviço (geralmente se cria um usuario com acesso restrito) 
		(não é nosso caso para o estudo não vamos alterar)
	4-agora no fim da tela Clique em Criar 
		Sua maquina virtual será criada.
		
			<<< Maquina virtual Criada>>>>
	
	Dica: para acessar sua maquina virtual vc pode acessar via ssh, na tela de instancias 
	na mesma linha que corresponde sua maquina a direita basta clicar em ssh, outra forma 
	de acesso é acessando via Cloudshell que fica localizado no canto direito superior é
	o quinto icone da direita para a esquerda, ao passar o mouse ele vai aparecer escrito 
	Cloudshell.
	
	a diferença é que esse Cloudshell é uma maquina disponibilizada pelo Google associada a 
	sua conta como se fosse outro desktop e vc nao precisa instalar nada para essa maquina 
	disponibilizada pelo google pois já é autenticada com seu usuario.
	voce pode testar isso pingando o ip interno <nao vai responder> e o ip externo do seu
	Servidor <ele vai responder>.
	
	
	Primeiro comando Shell
	
	gcloud compute instances list 
	
	Deve aparecer algo parecido com isso:
	
	NAME              ZONE                  MACHINE_TYPE  PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP    STATUS
instancia-hadoop  southamerica-east1-a  e2-medium                  10.158.0.2   34.95.249.216  RUNNING
	
	
	Depois crie o acesso ssh pelo Cloudshell digitando o seguinte comando:
	gcloud compute ssh instancia-hadoop --zone=southamerica-east1-a
	
	

Etapa 3 - Dataproc - Criando e Utilizando Cluster

1- Criação de Cluster Dataproc (console e SDK)
2- Primeiros passos no Dataproc
3- Rodando um Workload de teste no Cluster


Ativando o Serviço Dataproc
1-Para ativar a API Dataproc vc pode ir ate o menu API e buscar por "Cloud Dataproc API"
	ou vá ao menu Bigdata>>Dataproc e vc será redirecionado a API "Cloud Dataproc API"
	
2-Agora a partir do menu a esquerda selecione DATAPROC

3-Criar um Bucket.
	-Clique em Cloud Storage
	-Clique em Criar Intervalo 
	(muito cuidado ao criar o nome do bucket ele é unico e global para todo cliente do grupo GCP é unico)
	Tipo de local 
	[X] Multi-Region
	
	Local
	us (várias regioes nos Estados Unidos)
	Clique em <continue>
	
	Classe de Armazenamento deixe como 
	[X] Standard
	Clique em <continue>
	
	Controle de Acesso
	[x] Uniforme
	
	Criptografia
	[x] Chave de criptografia gerenciada pelo google
	
	clique em << Criar >>
	
	Bucket Criado!
	
4-Como Criar um Cluster
	-procure no campo de busca por DATAPROC 
	-Na tela do DATAPROC clique no botão superior "+ Criar Cluster"
	
	Para criar um cluster existem 4 etapas
		1-Configurar Cluster
			-Dê um nome ao cluster <<cluster-desafio-dataproc>>
			-Local Região e Zona (use opção default)
			-tipo de cluster
				
	utilizaremos >>>>Padrão 1 Mestre e N Works
				
				Nó Unico 	1 Mestre e 0 Works
				
				Alta  Disponibilidade 
							3 Mestres N Works
			
			-Control de versões vamos deixar como está.
			
			-Componentes 
				Selecione
				[x]Ativar Gateway de componentes.
				
			-Componentes opcionais
				[x] Jupyter Notebook
				[x] Zeppelin notebook
				[x] zookeeper
				
		
		2-Configurar nós  (onde escolhemos o tipo da maquina a ser utilizada)
			Toda configuração padrão
			Configure o Number of Workernodes para 3
			
		3-Personalizar Cluster
		<padrão nao alterar nada>
		
		4-Gerenciar Segurança
		<padrão nao alterar nada>
		
		
		<<Clique em Criar >>>
		
	
	
		Caso você fosse gerar toda a configuração via linha de comando seria:
				
		gcloud dataproc clusters create cluster-desafio-dataproc --enable-component-gateway 
		--region southamerica-east1 --zone southamerica-east1-a --master-machine-type n1-standard-4 
		--master-boot-disk-size 500 --num-workers 3 --worker-machine-type n1-standard-4 --worker-boot-disk-size 500
		 --image-version 2.0-debian10 --optional-components JUPYTER,ZEPPELIN,ZOOKEEPER --project desafiodataproc-321002





		Envie seu primeiro JOB
		1-Clique em DATAPROC
		2-Clique em JOBS
		3-Clique em envie um job
		4-Configure o nome do job
		5-Selecione a região que criou o cluster
		6-Selecione o cluster criado nos passos anteriores.
		7-Selecione o tipo de job Spark
		8-Configure a Classe  princial ou jar*
		Preencha "org.apache.spark.examples.SparkPI
		9- Configure os Arquivos jar.
			file:///usr/lib/spark/examples/jars/spark-examples.jar
		10-Argumento preencha 1000
		<clique em enviar>
		Pronto job enviado acompanhe


		Em linha de comando:
		gcloud dataproc jobs wait job-inicial2 --project desafiodataproc-321002 --region southamerica-east1

		ou
		
		gcloud dataproc jobs submit spark \
		 --cluster=cluster-desafio-dataproc \
		 --region="southamerica-east1" \
		 --class=org.apache.spark.examples.SparkPi \
		 --jars=file://usr/lib/spark/examples/jars/spark-examples.jar \
		 -- 1000
		
		
		Intruduçao a etapa 4
		1-Execução de workload no cluster Dataproc
		2-validação do Workload o cluster Dataproc
		3- Finalizar desafio
		
		
		Objetivo:
		git marcelomarques05/dio-desafio-dataproc
		
		[Repositorio do desafio] => [Cloud Storage] => [Cloud Dataproc] 
		=> [Cloud Storage] => [Seu repositorio resultado]
		
		
		
		
		
		
		
		
		
		
		
		
		




