# Importar e utilizar biblioteca no JUnit/Arquillian

Para conseguir utilizar uma biblioteca como, por exemplo, a *apache commons-email* em um teste JUnit executando com maven/arquillian, é preciso adicionar uma configuração no método anotado com `@Deployment`.

Nesse método, que retorna um objeto WebArchive, é necessário explicitamente adicionar a biblioteca carregando-a a partir do POM:

```java
@Deployment
public static WebArchive createDeployment() {
    PomEquippedResolveStage pom = Maven.resolver().loadPomFromFile("pom.xml");
    File[] commonsEmail = pom.resolve("org.apache.commons:commons-email").withTransitivity().asFile();

    WebArchive webArchive = ShrinkWrap.create(WebArchive.class, "test.war")
            .addPackage("br.com.empresa.dao")
            .addPackage("br.com.empresa.model")
            .addPackage("br.com.empresa.services")
            .addPackage("br.com.empresa.test.util")
            .addAsLibraries(commonsEmail)
            .addAsWebInfResource(EmptyAsset.INSTANCE, "beans.xml");

    System.out.println(webArchive.toString(true));
    return webArchive;
}
```

Foco nas instruções `pom.resolve` na linha 4 e `.addAsLibraries` de `ShrinkWrap.create`. Obviamente, a commons-email deve estar especificada nas dependências do pom.xml.