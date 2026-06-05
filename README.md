# Envio-autom-tico-de-e-mail

/**
 * Script para automação de resposta a fornecedores do HubSpot
 */
function responderFornecedoresAutomatico() {

  var termoBusca = '"formulário - fornecedores" is:unread';
  var threads = GmailApp.search(termoBusca);
  
  if (threads.length === 0) {
    console.log("Nenhum e-mail novo de fornecedor encontrado.");
    return;
  }

  for (var i = 0; i < threads.length; i++) {
    var mensagens = threads[i].getMessages();
    var ultimaMensagem = mensagens[mensagens.length - 1];
    var corpoTexto = ultimaMensagem.getPlainBody();
    
    var emailFornecedor = extrairEmail(corpoTexto);
    
    if (emailFornecedor) {
      console.log("Encontrado e-mail do fornecedor: " + emailFornecedor);
      
      var assuntoResposta = "Cadastro como fornecedor";
      var mensagemCorpo = "Olá,\n\nRecebemos seu interesse em se cadastrar como fornecedor.\n\n" +
                          "Para prosseguirmos com o seu cadastro no sistema, por favor, " +
                          "responda a este e-mail informando o CNPJ da sua empresa.\n\n" +
                          "Atenciosamente,\nSetor de Cadastro";
      
      GmailApp.sendEmail(emailFornecedor, assuntoResposta, mensagemCorpo);
      // ----------------------
      
      threads[i].markRead();
      console.log("E-mail enviado e processado com sucesso!");
    } else {
      console.log("Aviso: E-mail do fornecedor não identificado no texto.");
    }
  }
}

function extrairEmail(texto) {
  var regex = /[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}/g;
  var encontrados = texto.match(regex);
  
  if (encontrados) {
    for (var i = 0; i < encontrados.length; i++) {
      var e = encontrados[i].toLowerCase();
      if (e.indexOf("hubspot") === -1 && e.indexOf("prospect.com.br") === -1) {
        return encontrados[i];
      }
    }
  }
  return null;
}
