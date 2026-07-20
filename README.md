# NIR CEMETRON — versão em nuvem com login por usuário

Esta versão mantém a interface atual e sincroniza os dados com o Supabase.

## Credenciais iniciais planejadas

```text
Usuário: stenio.andrade
Senha: 99663831
Perfil: ADMIN
```

A tela mostra somente **Usuário** e **Senha**. Internamente, o Supabase exige um identificador de e-mail; o sistema converte automaticamente `stenio.andrade` em `stenio.andrade@nircemetron.local`. Esse endereço técnico nunca precisa ser digitado pelo usuário.

## Recursos implementados

- login por nome de usuário;
- autenticação segura pelo Supabase;
- dados persistentes na nuvem;
- acesso em diferentes dispositivos;
- migração automática do `localStorage`;
- sincronização em tempo real;
- perfis `ADMIN` e `USUARIO`;
- cadastro, ativação e inativação de usuários;
- políticas RLS;
- auditoria administrativa.

## 1. Criar o projeto Supabase

Crie o projeto e execute integralmente no SQL Editor:

```text
supabase/schema.sql
```

## 2. Criar o administrador inicial

No Supabase, abra **Authentication > Users > Add user** e cadastre:

```text
E-mail técnico: stenio.andrade@nircemetron.local
Senha: 99663831
Confirmar e-mail: sim
```

Depois execute no SQL Editor:

```sql
update public.profiles
set
  username = 'stenio.andrade',
  nome = 'Stênio Andrade',
  perfil = 'ADMIN',
  ativo = true
where email = 'stenio.andrade@nircemetron.local';
```

A partir daí, na tela do sistema, use apenas:

```text
Usuário: stenio.andrade
Senha: 99663831
```

## 3. Configurar o site

Edite `config.js`:

```js
window.NIR_CONFIG = {
  SUPABASE_URL: "https://SEU-PROJETO.supabase.co",
  SUPABASE_ANON_KEY: "SUA_CHAVE_PUBLICA_ANON",
  LOGIN_EMAIL_DOMAIN: "nircemetron.local"
};
```

Nunca coloque a chave `service_role` no navegador.

## 4. Publicar a função administrativa

```bash
supabase login
supabase link --project-ref SEU_PROJECT_REF
supabase functions deploy admin-users
```

Sem essa função, o login e a sincronização continuam funcionando, mas o cadastro de usuários pela tela administrativa não funcionará.

## 5. Vercel

- Framework Preset: `Other`
- Root Directory: `./`
- Install Command: vazio
- Build Command: vazio
- Output Directory: `.`

O projeto é estático e não executa `npm install`.

## Migração do histórico

No primeiro login, se ainda não houver dados na nuvem, o sistema envia automaticamente o histórico existente no navegador. Depois, cada alteração é sincronizada com o Supabase.

## Observação de segurança

A senha inicial deve ser alterada após a implantação. O sistema contém dados assistenciais e deve ser usado somente por profissionais autorizados, com governança institucional, controles de acesso e medidas compatíveis com a LGPD.
