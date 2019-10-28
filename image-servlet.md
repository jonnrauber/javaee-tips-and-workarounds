# Servlet de imagens em Java

Carregar dinamicamente no JSF imagens persistidas no banco de dados. Com esta solução é possível, por exemplo, carregar a foto do perfil do usuário salva no BD de uma aplicação.

Primeiro implementaremos o Servlet, que é a classe Java que responderá às requisições. Ela basicamente receberá um ID contendo o identificador para a imagem no banco de dados, buscará a imagem no banco e a converterá em uma resposta em bytes que será carregada no _browser_ cliente, exibindo a imagem. 

Código do Servlet:
```java
//requisições no formato /image?id={}
@WebServlet(urlPatterns = { "/image" })
public class ImageServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
	
    @Inject private DAO dao;

	public ImageServlet() {
	   super();
	}
	
	@Override
	protected void doGet(HttpServletRequest request, HttpServletResponse response)
	       throws ServletException, IOException {
	   try {
           File imagem = null;
	       try {
	           // buscar objeto File no banco de dados
               int id = Integer.parseInt(request.getParameter("id"));
               imagem = dao.recuperaImagem(id);

	       } catch (Exception e) {
	    	   // id incorreto vai receber a imagem "noimage"
	    	   // redirect para imagem default
	           response.sendRedirect(request.getContextPath() + "/resources/images/noimage.png");
	           throw e;
	       }
	       
	       String nomeArquivoImagem = imagem.getPath();
	     
	       // image/jpg, image/png
	       String contentType = this.getServletContext().getMimeType(nomeArquivoImagem);	     
	       response.setHeader("Content-Type", contentType);
	       response.setHeader("Content-Length", String.valueOf(imagem.length()));
	       response.setHeader("Content-Disposition", "inline; filename=\"" + nomeArquivoImagem + "\"");
	
	       // Converte o arquivo File para bytes e responde
	       byte[] bytes = new byte[(int) imagem.length()];
	       FileInputStream fis = new FileInputStream(imagem);
	       fis.read(bytes);
	       fis.close();
	       response.getOutputStream().write(bytes);
	
	   } catch (Exception e) {
	       throw new ServletException(e);
	   }
	}
	
	@Override
	protected void doPost(HttpServletRequest request, HttpServletResponse response)
	       throws ServletException, IOException {
	   doGet(request, response);
	}
}
```

Com este Servlet em mãos, é possível renderizar uma imagem dinamicamente no xhtml com o componente graphicImage:

```xhtml
<h:graphicImage value="/image?id=123" />
```