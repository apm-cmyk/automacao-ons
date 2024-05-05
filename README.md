# RPA Web No Nagevador Edge Com Java e Selenium

<p>Caro trabalhador deste país, você como eu sabe que a maioria das atividades executadas na labuta são tarefas monótonas, repetitivas e que carecem da necessidade de inteligência humana.</p>

<p align ="center">
<img src="https://github.com/apm-cmyk/automacao-ons/blob/main/Charlie_Chaplin_Tempos_Modernos_1936.gif"
</p>

Sendo assim uma forma de se livrar dessas tarefas para poder concentrar o seu tempo e esforços em atividades que realmente precisam de você, é automatizando esses processos. Automatizar nesse contexto significa ter um software programado para executar uma ou mais tarefas (abrir, baixar e fechar aquivos, pequisar na internet, acessar sites, tomar decições apartir de um critério e etc), com uma frequência de tempo e sem precisar de você.

Além de automatizar tarefas em aplicativos disponíveis em seu computador, você também pode automatizar o acesso ao seu navegador. Como exemplo, em tarefas em que é necessário buscar um dado disponível na Internet.

Preparei uma forma de fazer isso usando framework Selenium e a plataforma Java.

Nosso estudo de caso será 1 (um) acesso diário a página do ONS (Operador Nacional do Sistema Elétrico) diponível na Web para baixar o arquivo .xls com os dados de Linhas de Transmissão.

Em seguida criaremos um dashboard bem básico no Power BI, também automatizado, com esses dados do ONS. E por fim faremos uma validação do processo registrando o histórico através de uma variável temporal criada no próprio Power BI.

 
Para prosseguir você dverá conhecer o básico do básico de Java e ter instalado a IDE Eclipse no seu computador com o JDK funcionando.

Lembrando que isso pode ser feito com qualquer linguagem e em qualquer IDE desde que use os frameworks apropriados para a linguagem desejada.

## Passo 1 - Baixe Os arquivos Web Drive e Selenium

Você deverá baixar o arquvido Web Driver do navegador de sua preferência.
Abaixo estão disponíveis os links para baixar de alguns navegadores:

|Navegador|Site|
|---|---|
|Edge|[baixar]()|
|Chrome|[baixar]()|
|Mozila|[baixar]()|

Nesse tutorial conforme titulo vamos utilizar o navegador Edge.

## Passo 2 - Crie Um Projeto Java No Eclipse e Prepare O Ambiente

Após baixar o arquivo Web Drive do Edge, descompactue a pasta e coloque todo o conteúdo em uma diretório.
No meu caso eu criei um diretório Selenium/web-drives/edgedriver-win64, conforme imagem:


<img src="https://github.com/apm-cmyk/automacao-ons/blob/main/img/img1.png" alt="diretório criado">


No Eclipse crie um projeto Java, depois um pacote com o nome de sua preferência e adicione uma classe com o método main.



Com botão direito e o cursor em cima do projeto criado, vá para Build Path --> Configure Build Path e dentro do ClassPath adicione os aquivos JARs externo que vieram no arquivo Web Drive do Edge que você baixou:

<img src="https://github.com/apm-cmyk/automacao-ons/blob/main/img/img2.png" alt="diretório criado">



## Passo 3 - Classes e Métodos Básicos

```
package main;

import java.io.FileOutputStream;
import java.io.IOException;
import java.net.URL;
import java.nio.channels.Channels;
import java.nio.channels.ReadableByteChannel;
import java.time.Duration;
import java.time.LocalDate;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.edge.EdgeDriver;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;

public class AutomacaoONS {

	public static void main(String[] args) {

		//Criar variavel que armazene a data atual
		LocalDate dataHoje = LocalDate.now();

		// Especifique o local do driver do Edge (arquivo .exe baixado no web driver)
		System.setProperty("webdriver.edge.driver",
				"C:\\Program Files\\Selenium\\web-drivers\\edgedriver_win64\\msedgedriver.exe");

		// Inicialize o WebDriver
		WebDriver driver = new EdgeDriver();

		// Navegue até o site desejado
		driver.get("https://dados.ons.org.br/dataset/linha-transmissao");

		//Criar uma variavel WebElement que localize o elemento HTML pelo path
		WebElement pesquisar1 = driver.findElement(By.xpath("//*[@id=\"dataset-resources\"]/ul/li[3]/a"));
		pesquisar1.click();

		// Esperar até que o campo de pesquisa esteja visível e interagível
		WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(1));
		wait.until(ExpectedConditions
				.visibilityOfElementLocated(By.xpath("//*[@id=\"content\"]/div[3]/section/div/div[1]/ul/li/div/a")));


		//Criar uma variavel WebElement que localize o segundo elemento HTML pelo path
		WebElement pesquisar2 = driver.findElement(By.xpath("//*[@id=\"content\"]/div[3]/section/div/div[1]/ul/li/div/a"));
		// pesquisar2.click();


		/* Baixar o arquivo pela URL informada no elemento 2 */ 
		
		//Salvar a URL contida no botão
		String fileURL = pesquisar2.getAttribute("href");
		
		//Strig com o caminho do diretório que desejo salvar o arquivo
		String saveDir = "C:\\Users\\anapa\\OneDrive\\programação\\RPA\\automacao-ons";


		try {
			URL url = new URL(fileURL);
			ReadableByteChannel rbc = Channels.newChannel(url.openStream());
			FileOutputStream fos = new FileOutputStream(saveDir + "/LINHA_TRANSMISSAO_" + dataHoje + ".xlsx");
			fos.getChannel().transferFrom(rbc, 0, Long.MAX_VALUE);
			fos.close();
			rbc.close();
			System.out.println("Download concluído.");
		} catch (IOException e) {
			e.printStackTrace();
		}

		driver.quit();
	}
}


```


## Passo 4 - Criar Arquivo Final .jar

Para criar o arquivo .jar final com o botão direito no projeto vá para Export -> Runnable JAR File ->Next e em Launch configuration localize a sua classe Main().

Em Export destination indique o caminho ue você quer salvar o arquivo + nome_do_arquivo.jar.

Configure conforme imagem abaixo:

<img src="https://github.com/apm-cmyk/automacao-ons/blob/main/img/img3.png" alt="diretório criado">

Depois de finish o arquivo jar estará na pasta.
 
## Passo 5 - Agendador de Tarefas do Windows

Agora vamos usar o Agendador de Tarefas do Windows parar fazer com que esse arquivo .jar compile uma vez ao dia às 13:00.

No agendador de tarefas vá em Ação -> Criar Tarefa

Configure conforme imagens:

<img src="https://github.com/apm-cmyk/automacao-ons/blob/main/img/img4.png" alt="diretório criado">

<img src="https://github.com/apm-cmyk/automacao-ons/blob/main/img/img5.png" alt="diretório criado">

Em ações clique em Novo:

<img src="https://github.com/apm-cmyk/automacao-ons/blob/main/img/img6.png" alt="diretório criado">

Em Programa/script você deverá buscar o arquivo **java.exe** dentro da pasta da **JRE** ou da **JDK**.

Em adicione argumentos você deverá colocar o arquivo .jar do programa que você criou com o argumento -jar

```
-jar nome_do_arquivo.jar
```


Clique em OK e pronto sua tarefa estará criada e seu código rodará todos os dias no horário especificado.

Como ficou a pasta após dois dias de uso:

<img src="https://github.com/apm-cmyk/automacao-ons/blob/main/img/img7.png" alt="diretório criado">

Lembrando que ele foi executado quando a máquina estava ligada. 


## Passo 6 - Integração Com Dashboard Power BI


## Concluíndo...
Pronto! E não foi gasto nem dois neurônios aprendendo isso. 

Perceba que isso é um caso bem simples porém completo e que você pode aplicar a idéia em situações mais complexas de acordo com a sua necessidade.

