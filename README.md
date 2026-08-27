<!DOCTYPE html>
<html lang="pt-BR">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>Kanban MVP</title>

    <style>

        /* ==============================
           RESET
        ============================== */

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }


        /* ==============================
           BODY
        ============================== */

        body {

            min-height: 100vh;

            font-family: Arial, Helvetica, sans-serif;

            color: white;

            background:
                radial-gradient(
                    circle at 100% 0%,
                    #351080 0%,
                    transparent 28%
                ),

                radial-gradient(
                    circle at 0% 100%,
                    #4017c9 0%,
                    transparent 14%
                ),

                #151b4b;

            padding: 60px 5%;

            overflow-x: hidden;
        }


        /* ==============================
           DECORAÇÃO
        ============================== */

        body::before {

            content: "";

            position: fixed;

            width: 500px;
            height: 500px;

            top: -220px;
            left: -220px;

            border: 3px solid rgba(255,255,255,0.15);

            border-radius: 50%;

            box-shadow:
                0 0 0 45px transparent,
                0 0 0 48px rgba(255,255,255,0.10),
                0 0 0 95px transparent,
                0 0 0 98px rgba(255,255,255,0.07);

            pointer-events: none;
        }


        body::after {

            content: "";

            position: fixed;

            width: 500px;
            height: 500px;

            right: -250px;
            bottom: -250px;

            border: 3px solid rgba(255,255,255,0.12);

            border-radius: 50%;

            box-shadow:
                0 0 0 45px transparent,
                0 0 0 48px rgba(255,255,255,0.10),
                0 0 0 95px transparent,
                0 0 0 98px rgba(255,255,255,0.07);

            pointer-events: none;
        }


        /* ==============================
           KANBAN
        ============================== */

        .kanban {

            position: relative;

            z-index: 2;

            max-width: 1400px;

            margin: auto;

            display: grid;

            grid-template-columns:
                1fr
                1.15fr
                1fr;

            gap: 32px;
        }


        /* ==============================
           COLUNAS
        ============================== */

        .coluna {

            min-height: 665px;

            background: rgba(30,38,94,0.95);

            box-shadow:
                0 15px 40px rgba(0,0,0,0.20);

            transition: 0.3s;
        }


        .coluna:hover {

            transform: translateY(-3px);
        }


        /* ==============================
           CABEÇALHO
        ============================== */

        .cabecalho {

            padding: 25px 20px 20px;

            text-align: center;

            border-bottom:
                3px solid
                rgba(255,255,255,0.9);
        }


        .cabecalho h2 {

            font-size: clamp(27px, 3vw, 46px);

            font-weight: 900;

            letter-spacing: -1.5px;

            text-transform: uppercase;
        }


        /* ==============================
           CONTADOR
        ============================== */

        .contador {

            display: inline-flex;

            justify-content: center;

            align-items: center;

            width: 30px;
            height: 30px;

            margin-left: 8px;

            border-radius: 50%;

            background:
                rgba(255,255,255,0.13);

            font-size: 14px;

            vertical-align: middle;
        }


        /* ==============================
           ÁREA DAS TAREFAS
        ============================== */

        .tarefas {

            min-height: 560px;

            padding: 18px 20px 25px;

            transition: 0.2s;
        }


        .tarefas.drag-over {

            background:
                rgba(255,255,255,0.07);
        }


        /* ==============================
           TAREFA
        ============================== */

        .tarefa {

            display: flex;

            align-items: center;

            justify-content: space-between;

            gap: 12px;

            padding: 10px 0;

            margin-bottom: 8px;

            font-size: 20px;

            line-height: 1.4;

            cursor: grab;

            transition: 0.2s;
        }


        .tarefa:hover {

            transform:
                translateX(5px);
        }


        .tarefa:active {

            cursor: grabbing;
        }


        .tarefa.dragging {

            opacity: 0.35;

            transform:
                scale(0.97);
        }


        /* ==============================
           TEXTO DA TAREFA
        ============================== */

        .texto-tarefa {

            flex: 1;

            display: flex;

            align-items: flex-start;

            gap: 10px;
        }


        .texto-tarefa::before {

            content: "•";

            font-size: 27px;

            line-height: 20px;

            flex-shrink: 0;
        }


        /* Cores dos marcadores */

        #pendente .texto-tarefa::before {

            color: white;
        }


        #progresso .texto-tarefa::before {

            color: #8b8cff;
        }


        #concluido .texto-tarefa::before {

            color: #5ee6b2;
        }


        /* ==============================
           BOTÃO REMOVER
        ============================== */

        .btn-remover {

            flex-shrink: 0;

            width: 35px;
            height: 35px;

            padding: 0;

            border: none;

            border-radius: 7px;

            background:
                rgba(255,70,70,0.13);

            color: white;

            font-size: 16px;

            cursor: pointer;

            opacity: 0;

            transition: 0.2s;
        }


        .tarefa:hover .btn-remover {

            opacity: 1;
        }


        .btn-remover:hover {

            background: #e53935;

            transform:
                scale(1.08);
        }


        /* ==============================
           CONTROLES
        ============================== */

        .controles {

            position: relative;

            z-index: 5;

            max-width: 1400px;

            margin: 25px auto 0;

            display: flex;

            justify-content: center;

            gap: 12px;

            flex-wrap: wrap;
        }


        button {

            border: none;

            padding: 12px 22px;

            border-radius: 8px;

            background: #343ca0;

            color: white;

            font-size: 15px;

            font-weight: bold;

            cursor: pointer;

            transition: 0.2s;
        }


        button:hover {

            background: #4b54c9;

            transform:
                translateY(-2px);
        }


        /* ==============================
           MODAL
        ============================== */

        .modal {

            position: fixed;

            inset: 0;

            display: none;

            align-items: center;

            justify-content: center;

            background:
                rgba(4,7,25,0.78);

            z-index: 20;
        }


        .modal.active {

            display: flex;
        }


        .modal-box {

            width:
                min(420px, 90%);

            padding: 30px;

            background: #20275e;

            border:
                1px solid
                rgba(255,255,255,0.15);

            border-radius: 12px;

            box-shadow:
                0 25px 80px
                rgba(0,0,0,0.5);
        }


        .modal-box h3 {

            margin-bottom: 20px;

            font-size: 25px;
        }


        /* ==============================
           INPUT
        ============================== */

        input,
        select {

            width: 100%;

            padding: 13px;

            margin-bottom: 15px;

            border:
                1px solid
                rgba(255,255,255,0.20);

            border-radius: 7px;

            outline: none;

            background: #151b4b;

            color: white;

            font-size: 16px;
        }


        input:focus,
        select:focus {

            border-color: #7c83ff;
        }


        /* ==============================
           BOTÕES MODAL
        ============================== */

        .modal-buttons {

            display: flex;

            gap: 10px;
        }


        .modal-buttons button {

            flex: 1;
        }


        .cancelar {

            background: #3a3f62;
        }


        .cancelar:hover {

            background: #4b5073;
        }


        /* ==============================
           RESPONSIVO
        ============================== */

        @media (max-width: 900px) {

            body {

                padding:
                    30px 20px;
            }


            .kanban {

                grid-template-columns: 1fr;
            }


            .coluna {

                min-height: 400px;
            }


            .tarefas {

                min-height: 250px;
            }
        }


        @media (max-width: 500px) {

            .cabecalho h2 {

                font-size: 28px;
            }


            .tarefa {

                font-size: 17px;
            }


            .btn-remover {

                opacity: 1;
            }
        }

    </style>
</head>


<body>


    <!-- =================================
         KANBAN
    ================================== -->

    <main class="kanban">


        <!-- ===============================
             PENDENTE
        ================================ -->

        <section
            class="coluna"
            id="pendente"
        >

            <div class="cabecalho">

                <h2>

                    Pendente

                    <span class="contador">
                        0
                    </span>

                </h2>

            </div>


            <div
                class="tarefas"
                data-status="pendente"
            >

                <!-- Tarefas serão carregadas pelo JS -->

            </div>

        </section>


        <!-- ===============================
             EM PROGRESSO
        ================================ -->

        <section
            class="coluna"
            id="progresso"
        >

            <div class="cabecalho">

                <h2>

                    Em Progresso

                    <span class="contador">
                        0
                    </span>

                </h2>

            </div>


            <div
                class="tarefas"
                data-status="progresso"
            >

            </div>

        </section>


        <!-- ===============================
             CONCLUÍDO
        ================================ -->

        <section
            class="coluna"
            id="concluido"
        >

            <div class="cabecalho">

                <h2>

                    Concluído

                    <span class="contador">
                        0
                    </span>

                </h2>

            </div>


            <div
                class="tarefas"
                data-status="concluido"
            >

            </div>

        </section>


    </main>


    <!-- =================================
         BOTÕES
    ================================== -->

    <div class="controles">

        <button onclick="abrirModal()">

            + Adicionar tarefa

        </button>


        <button onclick="limparConcluidos()">

            🧹 Limpar concluídos

        </button>

    </div>


    <!-- =================================
         MODAL
    ================================== -->

    <div
        class="modal"
        id="modal"
    >

        <div class="modal-box">


            <h3>
                Nova tarefa
            </h3>


            <input
                type="text"
                id="novaTarefa"
                placeholder="Digite a tarefa..."
                maxlength="150"
            >


            <select id="statusTarefa">

                <option value="pendente">
                    Pendente
                </option>

                <option value="progresso">
                    Em Progresso
                </option>

                <option value="concluido">
                    Concluído
                </option>

            </select>


            <div class="modal-buttons">

                <button onclick="adicionarTarefa()">

                    Adicionar

                </button>


                <button
                    class="cancelar"
                    onclick="fecharModal()"
                >

                    Cancelar

                </button>

            </div>


        </div>

    </div>


    <!-- =================================
         JAVASCRIPT
    ================================== -->

    <script>


        /* =================================
           VARIÁVEIS
        ================================= */

        let tarefaArrastada = null;


        /* =================================
           CRIAR TAREFA
        ================================= */

        function criarTarefa(texto) {


            const tarefa =
                document.createElement("div");


            tarefa.className =
                "tarefa";


            tarefa.draggable = true;


            /* Texto */

            const textoTarefa =
                document.createElement("span");


            textoTarefa.className =
                "texto-tarefa";


            textoTarefa.textContent =
                texto;


            /* Botão remover */

            const botaoRemover =
                document.createElement("button");


            botaoRemover.className =
                "btn-remover";


            botaoRemover.innerHTML =
                "🗑️";


            botaoRemover.title =
                "Remover tarefa";


            botaoRemover.addEventListener(
                "click",
                function(event) {

                    event.stopPropagation();


                    const confirmar =
                        confirm(
                            "Tem certeza que deseja remover esta tarefa?"
                        );


                    if (!confirmar) {

                        return;

                    }


                    tarefa.remove();


                    atualizarContadores();

                    salvarDados();

                }
            );


            tarefa.appendChild(
                textoTarefa
            );


            tarefa.appendChild(
                botaoRemover
            );


            configurarDrag(tarefa);


            return tarefa;

        }


        /* =================================
           DRAG
        ================================= */

        function configurarDrag(tarefa) {


            tarefa.addEventListener(
                "dragstart",
                function() {

                    tarefaArrastada =
                        tarefa;

                    tarefa.classList.add(
                        "dragging"
                    );

                }
            );


            tarefa.addEventListener(
                "dragend",
                function() {

                    tarefa.classList.remove(
                        "dragging"
                    );


                    tarefaArrastada =
                        null;


                    document
                        .querySelectorAll(".tarefas")
                        .forEach(area => {

                            area.classList.remove(
                                "drag-over"
                            );

                        });


                    atualizarContadores();

                    salvarDados();

                }
            );

        }


        /* =================================
           ÁREAS DE DROP
        ================================= */

        document
            .querySelectorAll(".tarefas")
            .forEach(area => {


                area.addEventListener(
                    "dragover",
                    function(event) {

                        event.preventDefault();


                        area.classList.add(
                            "drag-over"
                        );

                    }
                );


                area.addEventListener(
                    "dragleave",
                    function() {

                        area.classList.remove(
                            "drag-over"
                        );

                    }
                );


                area.addEventListener(
                    "drop",
                    function(event) {

                        event.preventDefault();


                        area.classList.remove(
                            "drag-over"
                        );


                        if (!tarefaArrastada) {

                            return;

                        }


                        area.appendChild(
                            tarefaArrastada
                        );


                        atualizarContadores();

                        salvarDados();

                    }
                );

            });


        /* =================================
           CONTADORES
        ================================= */

        function atualizarContadores() {


            document
                .querySelectorAll(".coluna")
                .forEach(coluna => {


                    const quantidade =
                        coluna.querySelectorAll(
                            ".tarefa"
                        ).length;


                    const contador =
                        coluna.querySelector(
                            ".contador"
                        );


                    contador.textContent =
                        quantidade;

                });

        }


        /* =================================
           MODAL
        ================================= */

        function abrirModal() {


            document
                .getElementById("modal")
                .classList.add("active");


            document
                .getElementById("novaTarefa")
                .focus();

        }


        function fecharModal() {


            document
                .getElementById("modal")
                .classList.remove(
                    "active"
                );


            document
                .getElementById("novaTarefa")
                .value = "";

        }


        /* =================================
           ADICIONAR
        ================================= */

        function adicionarTarefa() {


            const input =
                document.getElementById(
                    "novaTarefa"
                );


            const status =
                document.getElementById(
                    "statusTarefa"
                ).value;


            const texto =
                input.value.trim();


            if (!texto) {


                alert(
                    "Digite uma tarefa!"
                );


                input.focus();


                return;

            }


            const tarefa =
                criarTarefa(texto);


            const area =
                document.querySelector(
                    `[data-status="${status}"]`
                );


            area.appendChild(
                tarefa
            );


            atualizarContadores();

            salvarDados();

            fecharModal();

        }


        /* =================================
           LIMPAR CONCLUÍDOS
        ================================= */

        function limparConcluidos() {


            const area =
                document.querySelector(
                    '[data-status="concluido"]'
                );


            const tarefas =
                area.querySelectorAll(
                    ".tarefa"
                );


            if (tarefas.length === 0) {


                alert(
                    "Não existem tarefas concluídas."
                );


                return;

            }


            const confirmar =
                confirm(
                    "Deseja remover todas as tarefas concluídas?"
                );


            if (!confirmar) {

                return;

            }


            area.innerHTML = "";


            atualizarContadores();

            salvarDados();

        }


        /* =================================
           SALVAR
        ================================= */

        function salvarDados() {


            const dados = {

                pendente: [],

                progresso: [],

                concluido: []

            };


            document
                .querySelectorAll(".tarefas")
                .forEach(area => {


                    const status =
                        area.dataset.status;


                    area
                        .querySelectorAll(".tarefa")
                        .forEach(tarefa => {


                            const texto =
                                tarefa
                                    .querySelector(
                                        ".texto-tarefa"
                                    )
                                    .textContent
                                    .trim();


                            dados[status].push(
                                texto
                            );

                        });

                });


            localStorage.setItem(
                "kanbanMVP",
                JSON.stringify(dados)
            );

        }


        /* =================================
           CARREGAR
        ================================= */

        function carregarDados() {


            const salvo =
                localStorage.getItem(
                    "kanbanMVP"
                );


            /* Primeiro acesso */

            if (!salvo) {


                criarDadosIniciais();

                atualizarContadores();

                return;

            }


            try {


                const dados =
                    JSON.parse(salvo);


                Object.keys(dados)
                    .forEach(status => {


                        const area =
                            document.querySelector(
                                `[data-status="${status}"]`
                            );


                        if (!area) {

                            return;

                        }


                        area.innerHTML = "";


                        dados[status]
                            .forEach(texto => {


                                const tarefa =
                                    criarTarefa(
                                        texto
                                    );


                                area.appendChild(
                                    tarefa
                                );

                            });

                    });


            }

            catch (erro) {


                console.error(
                    "Erro ao carregar Kanban:",
                    erro
                );


                criarDadosIniciais();

            }


            atualizarContadores();

        }


        /* =================================
           DADOS INICIAIS
        ================================= */

        function criarDadosIniciais() {


            const tarefasIniciais = {


                pendente: [

                    "Coleta de feedback dos usuários",

                    "Ajustes e melhorias do MVP",

                    "Adoção de práticas sustentáveis no MVP",

                    "Adoção de práticas inovadoras no MVP",

                    "Refinamento do MVP",

                    "Avaliação final do MVP"

                ],


                progresso: [

                    "Desenvolvimento do MVP"

                ],


                concluido: []

            };


            Object.keys(
                tarefasIniciais
            ).forEach(status => {


                const area =
                    document.querySelector(
                        `[data-status="${status}"]`
                    );


                tarefasIniciais[status]
                    .forEach(texto => {


                        const tarefa =
                            criarTarefa(
                                texto
                            );


                        area.appendChild(
                            tarefa
                        );

                    });

            });

        }


        /* =================================
           FECHAR MODAL CLICANDO FORA
        ================================= */

        document
            .getElementById("modal")
            .addEventListener(
                "click",
                function(event) {


                    if (
                        event.target.id ===
                        "modal"
                    ) {

                        fecharModal();

                    }

                }
            );


        /* =================================
           ENTER
        ================================= */

        document
            .getElementById("novaTarefa")
            .addEventListener(
                "keydown",
                function(event) {


                    if (
                        event.key ===
                        "Enter"
                    ) {

                        adicionarTarefa();

                    }


                    if (
                        event.key ===
                        "Escape"
                    ) {

                        fecharModal();

                    }

                }
            );


        /* =================================
           INICIAR
        ================================= */

        carregarDados();

    </script>

</body>

</html>