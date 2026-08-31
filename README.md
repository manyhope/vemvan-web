# vemvan-web

As duas páginas para onde os e-mails de autenticação do VemVan levam.

Elas existem porque o link dos e-mails apontava para `com.prometheus.vemvan://auth`,
que é endereço de aplicativo e não de página: no computador o navegador recebia o
redirecionamento e não tinha o que fazer com ele. Pior, o token era consumido com
sucesso — o `/auth/v1/verify` devolve `303` — e mesmo assim nada aparecia, então a
pessoa clicava de novo e só então lia "expirado".

- **`redefinir-senha/`** — recebe o `access_token` no fragmento da URL e troca a
  senha ali mesmo, em qualquer navegador.
- **`email-confirmado/`** — só confirma que deu certo; o `/verify` já liberou a
  conta antes de redirecionar para cá.

## Por que um repositório separado

O código do VemVan é privado, e GitHub Pages em repositório privado exige plano
pago. Aqui ficam apenas estes dois arquivos estáticos.

Hospedar dentro do Supabase não é possível: Edge Function e Storage devolvem
`text/plain` com `nosniff` e `Content-Security-Policy: default-src 'none'; sandbox`.
É a trava antiphishing da plataforma, verificada nos dois caminhos.

## Sobre a chave no código

A `sb_publishable_...` é publicável por definição — é a mesma que vai dentro do
APK. Quem autoriza a troca de senha é o `access_token` que chega no fragmento, e
ele vale uma única vez, com prazo. O fragmento nunca é enviado ao servidor, então
não aparece em log de acesso nem em `Referer`.
