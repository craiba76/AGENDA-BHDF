<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Agenda de Licitações</title>
    <style>
        table { width: 100%; border-collapse: collapse; }
        th, td { border: 1px solid black; padding: 8px; text-align: left; }
        th { background-color: #f2f2f2; }
        #loginForm { display: flex; flex-direction: column; width: 300px; margin: auto; }
        #agenda { display: none; }
        .destaque { background-color: yellow; }
    </style>
</head>
<body>
    <div id="login">
        <h2>Login</h2>
        <form id="loginForm">
            <input type="text" id="usuario" placeholder="Usuário" required>
            <input type="password" id="senha" placeholder="Senha" required>
            <button type="submit">Acessar</button>
        </form>
    </div>

    <div id="agenda">
        <h2>Agenda de Licitações</h2>
        <audio id="alertaSom" src="https://www.soundjay.com/button/beep-07.wav"></audio>
        <table id="tabela">
            <thead>
                <tr>
                    <th>Empresa</th>
                    <th>Pregão/Dispensa</th>
                    <th>UASG</th>
                    <th>Órgão</th>
                    <th>Estado</th>
                    <th>Portal</th>
                    <th>Descrição</th>
                    <th>Horário</th>
                    <th>Edital</th>
                    <th>Ação</th>
                </tr>
            </thead>
            <tbody></tbody>
        </table>
        <button onclick="ordenarTabela()">Ordenar por Data</button>
        <button onclick="adicionarNovaLinha()">Adicionar Nova Linha</button>
        <button onclick="fecharPlanilha()">Fechar e Salvar</button>
    </div>

    <script>
        function ordenarTabela() {
            let tabela = document.getElementById("tabela").getElementsByTagName("tbody")[0];
            let linhas = Array.from(tabela.rows);
            
            linhas.sort((a, b) => {
                let dataA = new Date(a.cells[7].querySelector("input").value);
                let dataB = new Date(b.cells[7].querySelector("input").value);
                return dataB - dataA;
            });
            
            linhas.forEach(linha => tabela.appendChild(linha));
            destacarPrimeiraLinha();
        }

        function destacarPrimeiraLinha() {
            let tabela = document.getElementById("tabela").getElementsByTagName("tbody")[0];
            Array.from(tabela.rows).forEach(row => row.classList.remove("destaque"));
            let earliestDateRow = null;
            let earliestDate = null;

            Array.from(tabela.rows).forEach(row => {
                let dateValue = new Date(row.cells[7].querySelector("input").value);
                if (!earliestDate || dateValue < earliestDate) {
                    earliestDate = dateValue;
                    earliestDateRow = row;
                }
            });

            if (earliestDateRow) {
                earliestDateRow.classList.add("destaque");
                earliestDateRow.style.backgroundColor = "green";
            }
        }

        const usuarios = { "BH": "123", "HDF": "321" };

        document.getElementById("loginForm").addEventListener("submit", function(event) {
            event.preventDefault();
            let usuario = document.getElementById("usuario").value.trim();
            let senha = document.getElementById("senha").value.trim();

            if (usuarios[usuario] && usuarios[usuario] === senha) {
                document.getElementById("login").style.display = "none";
                document.getElementById("agenda").style.display = "block";
                carregarDados();
                alert("Login bem-sucedido!");
            } else {
                alert("Usuário ou senha incorretos!");
            }
        });

        function salvarDados() {
            let dados = [];
            document.querySelectorAll("#tabela tbody tr").forEach(linha => {
                let obj = {};
                linha.querySelectorAll("input, select").forEach(input => {
                    obj[input.name] = input.value;
                });
                dados.push(obj);
            });
            localStorage.setItem("agendaLicitacoes", JSON.stringify(dados));
        }

        function carregarDados() {
            let dados = JSON.parse(localStorage.getItem("agendaLicitacoes")) || [];
            dados.forEach(dado => adicionarNovaLinha(dado));
        }

        function adicionarNovaLinha(dado = {}) {
            let tabela = document.getElementById("tabela").getElementsByTagName("tbody")[0];
            let novaLinha = tabela.insertRow();
            let colunas = ["empresa", "pregao", "uasg", "orgao", "estado", "portal", "descricao", "horario", "edital"];
            colunas.forEach(coluna => {
                let celula = novaLinha.insertCell();
                let input = document.createElement("input");
                input.type = coluna === "horario" ? "datetime-local" : "text";
                input.name = coluna;
                input.value = dado[coluna] || "";
                celula.appendChild(input);
            });
        }

        function fecharPlanilha() {
            salvarDados();
            alert("Planilha salva com sucesso!");
        }
    </script>
</body>
</html>
