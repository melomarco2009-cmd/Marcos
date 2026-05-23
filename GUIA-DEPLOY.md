# GUIA DEFINITIVO - Deploy MyFuckingLife na Vercel

## PASSO 1: Criar Banco de Dados (Neon)

1. Acesse https://neon.tech
2. Crie conta com Google/GitHub
3. Clique "New Project"
4. Nome: `myfuckinglife`
5. Copie a Connection String (exemplo):
   ```
   postgresql://usuario:senha@ep-xxx.us-east-1.aws.neon.tech/myfuckinglife?sslmode=require
   ```
6. Guarde essa URL - eh sua DATABASE_URL

---

## PASSO 2: Deploy na Vercel

### Metodo A - ZIP Upload (mais rapido)

1. Acesse https://vercel.com/new
2. Clique em **"Upload"** (botao ao lado de "Import Git Repository")
3. Selecione o arquivo:
   ```
   C:\Users\Marco\projects\myfuckinglife-deploy.zip
   ```
4. **Framework Preset**: `Next.js`
5. **Build Command**: deixe em branco (usa o do vercel.json)
6. Clique **"Deploy"**

### Metodo B - GitHub (recomendado para atualizacoes futuras)

1. Instale Git: https://git-scm.com/download/win
2. No CMD:
   ```cmd
   cd C:\Users\Marco\projects\myfuckinglife
   git init
   git add .
   git commit -m "Primeiro commit"
   ```
3. Crie repo em https://github.com/new
4. Siga as instrucoes do GitHub para fazer push
5. Na Vercel: "Import Git Repository" → selecione seu repo

---

## PASSO 3: Configurar Variaveis de Ambiente

No Vercel Dashboard → seu projeto → **Settings** → **Environment Variables**:

| Nome | Valor | Onde obter |
|---|---|---|
| DATABASE_URL | `postgresql://...` | Neon (Passo 1) |
| NEXTAUTH_URL | `https://seu-projeto.vercel.app` | URL do deploy Vercel |
| NEXTAUTH_SECRET | `JWl7BwrVNGayxadCmf/Y3T6w8FN58Z8fZCdED7ROubw=` | Ja geramos |
| ENCRYPTION_KEY | `30ad17d51c32bd4090ee969465617ed6514f85bada823846e7f0290fc7125cec` | Ja geramos |
| RESEND_API_KEY | (opcional) | resend.com - para email de reset funcionar |
| EMAIL_FROM | `MyFuckingLife <no-reply@seu-dominio.com>` | Seu email no Resend |

### Como obter a URL do deploy:
- Apos o primeiro deploy (mesmo que falhe), a Vercel mostra a URL
- Exemplo: `https://myfuckinglife-abc123.vercel.app`
- Cole essa URL em NEXTAUTH_URL

---

## PASSO 4: Rodar Migracao no Banco

No CMD (na sua maquina local):

```cmd
cd C:\Users\Marco\projects\myfuckinglife

set DATABASE_URL=postgresql://usuario:senha@ep-xxx.us-east-1.aws.neon.tech/myfuckinglife?sslmode=require

npx prisma db push
```

Substitua a DATABASE_URL pela sua do Neon.

---

## PASSO 5: Re-deploy

1. Vercel Dashboard → seu projeto → **Deployments**
2. Clique nos **tres pontos** do deploy mais recente
3. **"Redeploy"**
4. Aguarde o build completar

---

## PASSO 6: Acessar

A URL sera algo como:
```
https://myfuckinglife-abc123.vercel.app
```

---

## TROUBLESHOOTING

### Erro: "prisma: command not found"
- Causa: postinstall antigo no cache
- Solucao: O ZIP ja esta corrigido. Se persistir, use o botao "Redeploy" com "Use existing Build Cache" DESMARCADO

### Erro: "DATABASE_URL not found"
- Causa: Variavel de ambiente nao configurada
- Solucao: Verifique se DATABASE_URL esta em Settings > Environment Variables

### Erro: "404 NOT_FOUND"
- Causa: Build falhou
- Solucao: Verifique os logs em Deployments > clicar no deploy

### Erro: "Cannot find module '@prisma/client'"
- Causa: Prisma Client nao gerado
- Solucao: O buildCommand ja inclui `prisma generate`. Se falhar, adicione `prisma generate` como script adicional nas Build Settings

---

## LIMITACOES CONHECIDAS

- Uploads de arquivos funcionam em desenvolvimento
- Em producao (Vercel), uploads sao salvos em `/tmp` e podem ser perdidos entre deploys
- Para producao robusta, migre para Vercel Blob, Cloudinary ou AWS S3 no futuro

---

## CONTATO

Se tiver problemas, me envie o log completo do erro que eu ajudo a resolver.
