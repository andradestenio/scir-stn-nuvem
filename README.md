# NIR CEMETRON — versão em nuvem

Esta versão mantém a interface atual e substitui o armazenamento exclusivamente local por sincronização no Supabase.

## Recursos implementados

- autenticação por e-mail e senha;
- histórico persistente na nuvem;
- acesso aos mesmos dados em diferentes dispositivos;
- migração automática do histórico existente no `localStorage`;
- sincronização em tempo real;
- cópia local de segurança;
- perfis `ADMIN` e `USUARIO`;
- administração de usuários por função segura;
- políticas RLS no PostgreSQL;
- auditoria de criação, ativação e inativação de usuários.

## 1. Criar o projeto no Supabase

1. Acesse o Supabase e crie um projeto.
2. Abra **SQL Editor**.
3. Execute integralmente o arquivo `supabase/schema.sql`.

## 2. Criar o primeiro administrador

No Supabase:

1. Abra **Authentication > Users**.
2. Selecione **Add user > Create new user**.
3. Informe seu e-mail e uma senha segura.
4. Marque o e-mail como confirmado.

Depois, no **SQL Editor**, execute, substituindo o e-mail:

```sql
update public.profiles
set perfil = 'ADMIN', nome = 'Stênio Andrade', ativo = true
where email = 'SEU_EMAIL';
```

## 3. Configurar o site

Abra o arquivo `config.js` e substitua:

```js
window.NIR_CONFIG = {
  SUPABASE_URL: "https://SEU-PROJETO.supabase.co",
  SUPABASE_ANON_KEY: "SUA_CHAVE_PUBLICA_ANON"
};
```

As informações ficam em **Project Settings > API**.

A chave pública/anon pode ficar no navegador porque a segurança efetiva é aplicada pelas políticas RLS. Nunca coloque a chave `service_role` no `config.js`.

## 4. Implantar a função administrativa

A página de usuários depende da Edge Function `admin-users`.

Com a CLI do Supabase:

```bash
supabase login
supabase link --project-ref SEU_PROJECT_REF
supabase functions deploy admin-users
```

O Supabase fornece automaticamente as variáveis `SUPABASE_URL`, `SUPABASE_ANON_KEY` e `SUPABASE_SERVICE_ROLE_KEY` à função.

Sem essa função, login e sincronização funcionam normalmente; apenas o cadastro de usuários pela interface administrativa ficará indisponível.

## 5. Publicar no GitHub e Vercel

Envie todos os arquivos para a raiz do repositório.

Na Vercel:

- Framework Preset: `Other`
- Root Directory: `./`
- Install Command: vazio
- Build Command: vazio
- Output Directory: `.`

O projeto é estático e não executa `npm install`.

## Migração do histórico local

No primeiro login:

- se existir estado na nuvem, ele será carregado;
- se não existir estado na nuvem e houver histórico no navegador, o histórico local será enviado automaticamente;
- depois disso, todas as alterações são sincronizadas com o Supabase.

## Segurança assistencial

Como o sistema contém dados identificáveis de pacientes, configure:

- senhas individuais;
- acesso somente aos profissionais autorizados;
- revisão periódica dos usuários ativos;
- política institucional de privacidade e retenção;
- termo de responsabilidade;
- backups e plano de contingência;
- domínio restrito, quando aplicável.

Esta implementação fornece a base técnica, mas não substitui a avaliação institucional de LGPD, segurança da informação e governança clínica.
