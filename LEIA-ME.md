# Banca Examinadora — Concurso Fotográfico UnU
## Guia de configuração e deploy

---

## 1. Criar projeto no Firebase (gratuito)

1. Acesse **https://console.firebase.google.com**
2. Clique em **"Adicionar projeto"** → dê um nome (ex: `concurso-unu`)
3. Desative o Google Analytics (não precisa) → **Criar projeto**
4. No menu lateral, clique em **"Realtime Database"** → **"Criar banco de dados"**
5. Escolha **"Iniciar no modo de teste"** → selecione a região **us-central1** → **Ativar**

---

## 2. Pegar as credenciais do Firebase

1. No menu lateral, clique na engrenagem ⚙️ → **"Configurações do projeto"**
2. Role até **"Seus aplicativos"** → clique em **"</>"** (Web)
3. Dê um apelido (ex: `banca`) → **Registrar app**
4. Copie o bloco `firebaseConfig` que aparecer:

```js
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "concurso-unu.firebaseapp.com",
  databaseURL: "https://concurso-unu-default-rtdb.firebaseio.com",
  projectId: "concurso-unu",
  storageBucket: "concurso-unu.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

---

## 3. Colar as credenciais no index.html

Abra o `index.html` e encontre esta seção (linha ~400):

```js
const firebaseConfig = {
  apiKey: "COLE_SUA_API_KEY",
  authDomain: "SEU_PROJETO.firebaseapp.com",
  databaseURL: "https://SEU_PROJETO-default-rtdb.firebaseio.com",
  ...
};
```

Substitua pelos dados do passo 2.

---

## 4. Configurar regras do Firebase (segurança básica)

No Firebase Console → Realtime Database → **Regras**, cole:

```json
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

*(Modo aberto para uso interno da banca. Para mais segurança, configure autenticação depois.)*

---

## 5. Alterar a senha (opcional)

No `index.html`, encontre:

```js
const SENHA = "banca2025";
```

Troque por uma senha de sua escolha. Compartilhe apenas com os 3 jurados.

---

## 6. Alterar os nomes dos jurados (opcional)

No `index.html`, encontre:

```js
const JURADOS = ["Jurado 1", "Jurado 2", "Jurado 3"];
```

E no HTML, localize o `<select id="sel-jurado">` e altere as options:

```html
<option value="Profa. Ana Silva">Profa. Ana Silva</option>
<option value="Prof. Carlos Souza">Prof. Carlos Souza</option>
<option value="Profa. Maria Lima">Profa. Maria Lima</option>
```

⚠️ Os valores no `<select>` devem ser idênticos aos nomes no array `JURADOS`.

---

## 7. Deploy no Vercel (gratuito)

### Opção A — Via interface web (mais fácil)
1. Acesse **https://vercel.com** → crie uma conta gratuita
2. Clique em **"Add New Project"** → **"Upload"** (ou conecte ao GitHub)
3. Faça upload da pasta `concurso-fotos/`
4. Clique em **Deploy** → em ~1 minuto seu link estará pronto

### Opção B — Via GitHub
1. Crie um repositório no GitHub com os arquivos
2. No Vercel, importe o repositório
3. Deploy automático a cada push

---

## 8. Configurar as fotos

Depois do deploy, acesse o app com a URL do Vercel:

1. Faça login com qualquer jurado + senha
2. Clique em **"Painel admin"** no canto superior direito
3. Clique em **"Configurar fotos"**
4. Cole a URL de cada foto (36 URLs no total)

### Formato de URL recomendado para Google Drive:
```
https://drive.google.com/uc?export=view&id=ID_DO_ARQUIVO
```
Para pegar o ID: clique com botão direito na imagem no Drive → **"Compartilhar"** → copie o link. O ID é a parte entre `/d/` e `/view`.

---

## 9. Como usar no dia da avaliação

1. Envie o link do Vercel para os 3 jurados
2. Cada jurado escolhe seu nome e entra com a senha
3. Avalia foto por foto — as respostas salvam automaticamente
4. Você acompanha o progresso em tempo real no **Painel admin**

---

## Estrutura de dados no Firebase

```
evals/
  Jurado 1/
    0/ → { q1: [false,false,false,false,false,true], q2: "sim", q3: "sim", q4: "sim" }
    1/ → { q1: [true,false,...], q2: null, q3: null, q4: null }
    ...
  Jurado 2/
    ...
config/
  urls/ → ["https://...", "https://...", ...]  (36 URLs)
```

---

## Critérios de status no painel

| Status | Critério |
|---|---|
| **Aprovada** | Q4 = Sim pelos 3 jurados |
| **Desclassificada** | Qualquer jurado marcou item 1–5 na P1 |
| **Divergente** | 3 jurados avaliaram, mas Q4 não foi unânime |
| **Pendente** | Nem todos os jurados avaliaram ainda |
