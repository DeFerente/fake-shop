1 - Criar cluster no Digital Ocean antes de começar a configuração do pipeline.

2 - Pipeline CI/CD será configurado no mesmo arquivo.

3- Utilizando o GitHub Actions para criar o pipeline CI/CD para automação das atualizações do ecommerce FakeShop.

4 - Para iniciar o processo é preciso ir até o repositório do projeto fake-shop no GitHub.

5 - Após isso, clique em Actions para acessar o GitHub Actions e iniciar a criação do workflow clicando na mensagem “set up a workflow yourself”.

6 - Com a tela do arquivo workflow aberta, é hora de especificar o nome do workflow e qual trilha/evento será acionada(o) pelo workflow.

7 - O próximo passo é apontar o branch que será executado toda vez que for dado push no workflow.

8 - A seguir definimos os jobs para CI; as ações que serão executadas pelo workflow e em qual ordem, assim como qual ambiente (Linux, Windows ou MacOs) que será usado para executar essas ações.

9 - Logo abaixo é preciso especificar os steps (passo a passo) das ações dos jobs.

10 - Primeiro step é usado a action checkout@v4.2.2 para que seja possível acessar o código do projeto no repositório.

11 - Próximo step é especificado para logar no Docker Hub usando a action docker/login-action@v3.
12 - Em conjunto com a ação de logar no Docker Hub, é preciso especificar o username e o password. Para isso é usado username: ${{ secrets.DOCKERHUB_USERNAME }} e password: ${{ secrets.DOCKERHUB_TOKEN }} para acessar informações armazenadas no GitHub pela opção secret em Settings > Secrets > Actions > New repository secret no GitHub.

13 - A seguir é usado a action docker/build-push-action@v6 para dar build e push nas imagens fake-shop que está no Docker Hub. É usado como referência file: https://raw.githubusercontent.com/deferente/fake-shop/main/Dockerfile para encontrar o Docker file, que no caso desse projeto, se encontra no repositório fake-shop do GitHub do usuário DeFerente.

14 - E por último, para finalizar as ações do CI, é declarado o nome da imagem em  tags: deferente/fake-shop:v${{ github.run_number }}.

15 - Agora é iniciado o pipeline do CD declarando needs: [CI], ou seja, as ações especificadas em CD só serão iniciadas uma vez que o pipeline de CI foi executado e completado.

16 - Iniciamos declarando qual ambiente (Linux, Windows ou MacOs) será usado para executar as ações do pipeline CD.

17 - Próximo step usamos a action checkout@v4.2.2 para que seja possível acessar o código do projeto no repositório.

18 - A seguir é preciso configurar o arquivo kubeconfig para que seja possível acessar o cluster Kubernetes criado na Digital Ocean. Usamos a action azure/k8s-set-context@v4 com o method: kubeconfig e kubeconfig: ${{ secrets.K8S_KUBECONFIG }} para fazer isso. É necessário lá em Settings > Secrets > Actions > New repository secret configurar Name* K8S_KUBECONFIG e em Secret* todo o conteúdo do config file do cluster Kubernetes.

19 - Por último, é preciso especificar o apply no manifesto deployment.yaml. Para isso usamos a action Azure/k8s-deploy@v5 declarando manifests: |  https://raw.githubusercontent.com/DeFerente/fake-shop/refs/heads/main/deployment.yaml (que se encontra no repositório fake-shop do GitHub do usuário DeFerente) e  images: | deferente/fake-shop:v${{ github.run_number }}.

20 . Terminado de configurar o pipeline CI/CD, é hora de commit e push para fazer o deploy.
