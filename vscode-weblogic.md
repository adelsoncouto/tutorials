## Plugins a serem instalados
O plugin que da suporte a java no vscode e baseado em bibliotecas do eclipse. O auto-complete e drill-down são muito semelhantes com algumas melhorias (preview de código direto na tela, busca de arquivos mais simples e mais completa, Preview de replace em arquivos, etc)

1. Instalar pacotes de plugin java: [vscjava.vscode-java-pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack#overview)
1. De uma olhada no marketplace por outros plugins q lhe interesse (snippets, auto complete especifico pra alguma coisa, etc)

## Configurar depuração remota no weblogic

Vale ressaltar que essa configuração é semelhante para a maioria dos servidores de aplicação (jetty, apache, jboss, etc).
Em todos você tem que encontrar uma forma de setar a variável de ambiente JAVA_OPTIONS com os valores de debug. Isso pode ser feito diretamente nas variáveis de ambiente do sistema (o problema dessa alternativa e que toda e qualquer aplicação java vai iniciar sempre no modo depuração)

1. Procurar pela pasta WEBLOGIC_HOME 
   - lugar onde weblogic foi instalado
   - exe: C:\javaTools\wls12212
1. Apartir da pasta do weblogic entrar em user_projects\domains\{nome do seu dominio}\bin
1. Copiar arquivo startWebLogic 
   - nome do arquivo no 
     - windows: startWebLogic.cmd
     - linux: startWebLogic.sh
1. Renomear para startWebLogicDebug.cmd(windows) ou startWebLogicDebug.sh(linux)
1. Inserir linha set JAVA_OPTIONS=-Xdebug -Xnoagent -Xrunjdwp:transport=dt_socket,address=4000,server=y,suspend=n
   - para mais detalhes ver [Remote Debugging an EJB on WebLogic Server 10.0.1 with JDeveloper 10.1.3.3](http://www.oracle.com/technetwork/developer-tools/jdev/remotedebugwls-086628.html)
   - note que *address=4000* é a porta que será usada para depuração. Caso alguma aplicação já use esta porta. Altere
1. Execute o novo arquivo que você criou. Isso irá iniciar o weblogic em modo de depuração.
1. Publique sua aplicação do weblogic (caso ela não se publique automaticamente com maven install)
1. Reinicie o vscode. Os plugins já devem ter sido instalados. E é necessário reiniciar para eles serem ativados
1. Abra o projeto maven a partir da primeiro arquivo pom.xml

## criar configuração de debug no vscode 

É necessário criar o arquivo de configuração launch.json. Ele serve para iniciar vários tipos de aplicação no vscode. Esse arquivo pode ser gerado automaticamente só que vou deixar instruções específicas de como ele deve ficar

1. Se nao existir a pasta ".vscode" no projeto crie-a 
   - Interessante inserir o arquivo no .gitignore caso nao queira q ele seja comitado no git
1. Dentro da pasta criar o arquivo launch.json
   - Lempre-se de alterar o atributo "port" caso tenha alterado ele na configuração do weblogic
   ``` json
    {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
        "version": "0.2.0",
        "configurations": [
            //(Opcional)
            //Depurar uma classe com public static void main. Depurar java local
            {
                "type": "java",
                "name": "Debug (Launch)",
                "request": "launch",
                "mainClass": "seu.name.space.aqui.classe.com.public.static.void.main",
                "args": ""
            },
            //(weblogic)
            {
                "type": "java",
                "name": "Debug (Attach)",
                "request": "attach",
                "hostName": "localhost",
                "port": 4000
            }
        ]
    }
   ```
- Aperte play (F5) e escolha a opção Attach
- A depuração deve funcionar, a barra inferior fica laranja quando o depurador remoto "atacha" no weblogic
- espalhe pontos de depuração e seja feliz. Alguns podem "demorar" pra ativar. Isso acontece geralmente com depuração remota.
- note que a mesma técnica pode ser usada para depurar servidores em ambientes de homologação e produção. Facilitando em muito a vida de quem precisa resolver o problema pra ontem.
   
   Fonte: https://github.com/lourencomcviana/presentation/wiki/Como-desenvolver-java-usando-weblogic-no-vscode

 
