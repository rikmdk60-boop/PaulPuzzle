<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Paul Puzzle | Jogo dos 15 Níveis</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,300;14..32,400;14..32,600;14..32,700;14..32,800&display=swap" rel="stylesheet">
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        primary: '#6366F1',
                        primaryDark: '#4F46E5',
                        secondary: '#EC4899',
                        dark: '#0F172A',
                        card: '#1E293B',
                        success: '#10B981',
                        warning: '#F59E0B',
                        danger: '#EF4444',
                    },
                    fontFamily: {
                        'sans': ['Inter', 'sans-serif'],
                    },
                    animation: {
                        'float': 'float 6s ease-in-out infinite',
                        'glow': 'glow 2s ease-in-out infinite',
                        'slide-up': 'slideUp 0.3s ease-out',
                    },
                    keyframes: {
                        float: {
                            '0%, 100%': { transform: 'translateY(0px) rotate(0deg)' },
                            '50%': { transform: 'translateY(-20px) rotate(5deg)' },
                        },
                        glow: {
                            '0%, 100%': { boxShadow: '0 0 5px rgba(99, 102, 241, 0.3)' },
                            '50%': { boxShadow: '0 0 25px rgba(99, 102, 241, 0.6)' },
                        },
                        slideUp: {
                            '0%': { opacity: '0', transform: 'translateY(20px)' },
                            '100%': { opacity: '1', transform: 'translateY(0)' },
                        },
                    },
                }
            }
        }
    </script>
    <style>
        * {
            user-select: none;
            -webkit-tap-highlight-color: transparent;
        }
        
        .bg-animate {
            background: linear-gradient(135deg, #0F172A 0%, #1E1B4B 25%, #2E1065 50%, #1E1B4B 75%, #0F172A 100%);
            background-size: 400% 400%;
            animation: gradientFlow 15s ease infinite;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: -2;
        }
        
        @keyframes gradientFlow {
            0% { background-position: 0% 0%; }
            50% { background-position: 100% 100%; }
            100% { background-position: 0% 0%; }
        }
        
        .particles {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: -1;
            pointer-events: none;
        }
        
        .particle {
            position: absolute;
            background: rgba(99, 102, 241, 0.15);
            border-radius: 50%;
            pointer-events: none;
            animation: particleFloat 20s infinite linear;
        }
        
        @keyframes particleFloat {
            0% {
                transform: translateY(100vh) rotate(0deg);
                opacity: 0;
            }
            10% {
                opacity: 0.8;
            }
            90% {
                opacity: 0.8;
            }
            100% {
                transform: translateY(-100vh) rotate(720deg);
                opacity: 0;
            }
        }
        
        .grid-container {
            background: rgba(15, 23, 42, 0.6);
            backdrop-filter: blur(10px);
            border-radius: 1.5rem;
            padding: 1rem;
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
            border: 1px solid rgba(99, 102, 241, 0.2);
        }
        
        .tile {
            background: linear-gradient(145deg, #1E293B, #0F172A);
            border-radius: 0.75rem;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.75rem;
            font-weight: 800;
            cursor: pointer;
            transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.3);
            border: 1px solid rgba(99, 102, 241, 0.2);
        }
        
        .tile:active {
            transform: scale(0.95);
        }
        
        .tile.empty {
            background: rgba(15, 23, 42, 0.5);
            cursor: default;
            box-shadow: none;
            border: 1px dashed rgba(99, 102, 241, 0.3);
        }
        
        .tile.correct {
            background: linear-gradient(145deg, #10B981, #059669);
            box-shadow: 0 0 15px rgba(16, 185, 129, 0.4);
            border-color: rgba(16, 185, 129, 0.6);
        }
        
        .tile.moving {
            transform: scale(1.1);
            background: linear-gradient(145deg, #6366F1, #4F46E5);
            box-shadow: 0 0 25px rgba(99, 102, 241, 0.8);
        }
        
        .level-up {
            animation: levelPulse 0.6s ease-out;
        }
        
        @keyframes levelPulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); text-shadow: 0 0 20px #6366F1; }
            100% { transform: scale(1); }
        }
        
        .xp-bar-fill {
            transition: width 0.5s cubic-bezier(0.4, 0, 0.2, 1);
        }
        
        button {
            transition: all 0.2s ease;
        }
        
        button:active {
            transform: scale(0.97);
        }
    </style>
</head>
<body class="font-sans text-white min-h-screen flex items-center justify-center p-4 relative">

    <div class="bg-animate"></div>
    <div class="particles" id="particles"></div>

    <div class="w-full max-w-lg animate-slide-up">
        <!-- Header principal -->
        <div class="text-center mb-6">
            <div class="flex items-center justify-center gap-3 mb-2">
                <i class="fa fa-puzzle-piece text-3xl text-primary"></i>
                <h1 class="text-4xl md:text-5xl font-extrabold bg-gradient-to-r from-primary via-secondary to-primary bg-clip-text text-transparent">
                    PAUL PUZZLE
                </h1>
                <i class="fa fa-trophy text-3xl text-warning"></i>
            </div>
            <p class="text-slate-300 text-sm">Domine todos os níveis | Clássico dos 15 números</p>
        </div>

        <!-- Painel de Status Principal -->
        <div class="bg-card/80 backdrop-blur-md rounded-2xl p-5 mb-5 border border-primary/20 shadow-xl">
            <!-- Nível e XP -->
            <div class="flex justify-between items-center mb-3">
                <div class="flex items-center gap-2">
                    <i class="fa fa-star text-warning"></i>
                    <span class="text-sm font-semibold text-slate-300">NÍVEL</span>
                    <span id="nivelAtual" class="text-2xl font-bold text-primary">1</span>
                </div>
                <div class="flex items-center gap-2">
                    <i class="fa fa-flag-checkered text-success"></i>
                    <span class="text-sm font-semibold text-slate-300">RECORDE</span>
                    <span id="recordeMovimentos" class="text-lg font-bold text-white">--</span>
                </div>
            </div>
            
            <!-- Barra de XP Profissional -->
            <div class="mb-3">
                <div class="flex justify-between text-xs text-slate-400 mb-1">
                    <span><i class="fa fa-flask mr-1"></i>XP</span>
                    <span><span id="xpAtual">0</span> / <span id="xpNecessario">100</span></span>
                </div>
                <div class="h-3 bg-slate-700/50 rounded-full overflow-hidden">
                    <div id="barraXp" class="xp-bar-fill h-full bg-gradient-to-r from-primary to-secondary rounded-full" style="width: 0%"></div>
                </div>
            </div>

            <!-- Stats do Jogo -->
            <div class="grid grid-cols-2 gap-3">
                <div class="bg-dark/50 rounded-xl p-2 text-center">
                    <i class="fa fa-arrows-alt text-primary text-sm"></i>
                    <p class="text-xs text-slate-400 mt-1">MOVIMENTOS</p>
                    <p id="contador" class="text-xl font-bold text-white">0</p>
                </div>
                <div class="bg-dark/50 rounded-xl p-2 text-center">
                    <i class="fa fa-hourglass-half text-primary text-sm"></i>
                    <p class="text-xs text-slate-400 mt-1">TEMPO</p>
                    <p id="tempo" class="text-xl font-bold text-white">00:00</p>
                </div>
            </div>
        </div>

        <!-- Tabuleiro do Jogo -->
        <div class="grid-container mb-5">
            <div id="tabuleiro" class="grid grid-cols-4 gap-2.5 aspect-square"></div>
        </div>

        <!-- Mensagem de Vitória com Animação -->
        <div id="mensagemVitoria" class="hidden bg-gradient-to-r from-success/20 to-primary/20 backdrop-blur-lg border border-success/50 rounded-xl p-4 text-center mb-4 transition-all duration-300">
            <div class="flex items-center justify-center gap-2 mb-1">
                <i class="fa fa-trophy text-3xl text-warning"></i>
                <h3 class="text-xl font-bold text-success">VITÓRIA!</h3>
                <i class="fa fa-trophy text-3xl text-warning"></i>
            </div>
            <p class="text-slate-200 text-sm">Você completou o nível em <span id="finalMovimentos" class="font-bold text-primary"></span> movimentos</p>
            <p class="text-slate-300 text-xs mt-1">+<span id="xpGanho"></span> XP conquistado</p>
            <button id="proximoNivel" class="mt-3 bg-primary hover:bg-primaryDark px-6 py-2 rounded-lg font-semibold text-sm transition-all">
                ⚡ PRÓXIMO NÍVEL ⚡
            </button>
        </div>

        <!-- Botões de Ação -->
        <div class="flex gap-3">
            <button id="botaoReiniciar" class="flex-1 bg-warning/20 hover:bg-warning/40 border border-warning/30 rounded-xl py-3 font-semibold transition-all flex items-center justify-center gap-2">
                <i class="fa fa-refresh"></i> Reiniciar Nível
            </button>
            <button id="botaoResetTotal" class="flex-1 bg-danger/20 hover:bg-danger/40 border border-danger/30 rounded-xl py-3 font-semibold transition-all flex items-center justify-center gap-2">
                <i class="fa fa-trash"></i> Resetar Progresso
            </button>
        </div>
        
        <div class="text-center text-xs text-slate-500 mt-5">
            <i class="fa fa-lightbulb-o text-primary"></i> Clique nos números adjacentes ao espaço vazio
        </div>
    </div>

    <script>
        // ---------- SISTEMA DE NÍVEIS E XP ----------
        let nivel = 1;
        let xpAtual = 0;
        let recordeMovimentos = localStorage.getItem('recordeMovimentos') ? parseInt(localStorage.getItem('recordeMovimentos')) : null;
        
        // Dados do jogo
        let numeros = [];
        let vazioPos = 15;
        let movimentos = 0;
        let tempoSegundos = 0;
        let cronometro;
        let jogoFinalizado = false;
        let xpGanhoNivel = 0;

        // Elementos
        const tabuleiroEl = document.getElementById('tabuleiro');
        const contadorEl = document.getElementById('contador');
        const tempoEl = document.getElementById('tempo');
        const mensagemVitoria = document.getElementById('mensagemVitoria');
        const botaoReiniciar = document.getElementById('botaoReiniciar');
        const botaoResetTotal = document.getElementById('botaoResetTotal');
        const botaoProximo = document.getElementById('proximoNivel');
        const finalMovimentosEl = document.getElementById('finalMovimentos');
        const xpGanhoEl = document.getElementById('xpGanho');
        const nivelAtualSpan = document.getElementById('nivelAtual');
        const xpAtualSpan = document.getElementById('xpAtual');
        const xpNecessarioSpan = document.getElementById('xpNecessario');
        const barraXpDiv = document.getElementById('barraXp');
        const recordeMovimentosSpan = document.getElementById('recordeMovimentos');

        // Atualizar UI de nível e XP
        function atualizarInterfaceProgresso() {
            nivelAtualSpan.textContent = nivel;
            const xpNecessario = calcularXpNecessario();
            xpNecessarioSpan.textContent = xpNecessario;
            xpAtualSpan.textContent = xpAtual;
            const percentual = (xpAtual / xpNecessario) * 100;
            barraXpDiv.style.width = `${percentual}%`;
            
            if (recordeMovimentos !== null) {
                recordeMovimentosSpan.textContent = recordeMovimentos;
            } else {
                recordeMovimentosSpan.textContent = '---';
            }
        }

        function calcularXpNecessario() {
            return 50 + (nivel - 1) * 25;
        }

        // Adicionar XP e verificar up de nível
        function adicionarXp(quantidade) {
            xpAtual += quantidade;
            let xpNecessario = calcularXpNecessario();
            let subiuNivel = false;
            
            while (xpAtual >= xpNecessario && !jogoFinalizado) {
                xpAtual -= xpNecessario;
                nivel++;
                subiuNivel = true;
                xpNecessario = calcularXpNecessario();
                
                // Efeito visual de up
                const nivelEl = document.getElementById('nivelAtual');
                nivelEl.classList.add('level-up');
                setTimeout(() => nivelEl.classList.remove('level-up'), 600);
                
                // Mensagem rápida flutuante
                mostrarNotificacao(`🎉 UP! Nível ${nivel} alcançado! 🎉`, 'success');
            }
            
            if (subiuNivel) {
                // Salvar progresso
                salvarProgresso();
                atualizarInterfaceProgresso();
                // Sortear novo tabuleiro com dificuldade maior? mantém mas reseta o jogo atual
                if (!jogoFinalizado) {
                    reiniciarJogoAtual();
                }
            } else {
                atualizarInterfaceProgresso();
            }
        }
        
        function mostrarNotificacao(msg, tipo) {
            // Criar toast simples
            const toast = document.createElement('div');
            toast.className = `fixed top-20 left-1/2 transform -translate-x-1/2 bg-${tipo === 'success' ? 'success' : 'primary'} text-white px-5 py-2 rounded-full text-sm font-semibold shadow-lg z-50 animate-slide-up`;
            toast.innerHTML = msg;
            document.body.appendChild(toast);
            setTimeout(() => toast.remove(), 2000);
        }
        
        // Salvar progresso no localStorage
        function salvarProgresso() {
            localStorage.setItem('puzzleNivel', nivel);
            localStorage.setItem('puzzleXp', xpAtual);
            if (recordeMovimentos) {
                localStorage.setItem('recordeMovimentos', recordeMovimentos);
            }
        }
        
        function carregarProgresso() {
            const savedNivel = localStorage.getItem('puzzleNivel');
            const savedXp = localStorage.getItem('puzzleXp');
            if (savedNivel) nivel = parseInt(savedNivel);
            if (savedXp) xpAtual = parseInt(savedXp);
            if (recordeMovimentos === null && localStorage.getItem('recordeMovimentos')) {
                recordeMovimentos = parseInt(localStorage.getItem('recordeMovimentos'));
            }
            atualizarInterfaceProgresso();
        }
        
        // Reset completo
        function resetarProgressoTotal() {
            if (confirm('⚠️ ATENÇÃO: Isso resetará TODO seu progresso, níveis e recordes. Deseja continuar?')) {
                nivel = 1;
                xpAtual = 0;
                recordeMovimentos = null;
                localStorage.removeItem('puzzleNivel');
                localStorage.removeItem('puzzleXp');
                localStorage.removeItem('recordeMovimentos');
                atualizarInterfaceProgresso();
                iniciarJogo();
                mostrarNotificacao('Progresso resetado! Comece do nível 1', 'warning');
            }
        }
        
        // Embaralhar respeitando paridade (solucionável)
        function embaralharSolucionavel(arr) {
            do {
                for (let i = arr.length - 1; i > 0; i--) {
                    const j = Math.floor(Math.random() * (i + 1));
                    [arr[i], arr[j]] = [arr[j], arr[i]];
                }
            } while (!isSolucionavel(arr));
        }
        
        function isSolucionavel(arr) {
            let inversoes = 0;
            const linear = arr.filter(v => v !== null);
            for (let i = 0; i < linear.length; i++) {
                for (let j = i + 1; j < linear.length; j++) {
                    if (linear[i] > linear[j]) inversoes++;
                }
            }
            const posVazio = arr.indexOf(null);
            const linhaVazio = Math.floor(posVazio / 4);
            return (inversoes % 2 === 0) === ((4 - linhaVazio) % 2 === 0);
        }
        
        // Iniciar/reiniciar jogo para nível atual
        function iniciarJogo() {
            if (cronometro) clearInterval(cronometro);
            
            numeros = [];
            movimentos = 0;
            tempoSegundos = 0;
            jogoFinalizado = false;
            mensagemVitoria.classList.add('hidden');
            contadorEl.textContent = '0';
            tempoEl.textContent = '00:00';
            
            for (let i = 1; i <= 15; i++) numeros.push(i);
            numeros.push(null);
            embaralharSolucionavel(numeros);
            vazioPos = numeros.indexOf(null);
            
            // Dificuldade extra baseada no nível (aumenta embaralhamento, mas mantém resolvível)
            for (let extra = 0; extra < Math.min(nivel - 1, 30); extra++) {
                const possiveis = [];
                for (let i = 0; i < 16; i++) {
                    if (i !== vazioPos && Math.abs(i - vazioPos) === 4 || (Math.abs(i - vazioPos) === 1 && Math.floor(i/4) === Math.floor(vazioPos/4))) {
                        possiveis.push(i);
                    }
                }
                if (possiveis.length > 0) {
                    const randomIndex = possiveis[Math.floor(Math.random() * possiveis.length)];
                    [numeros[randomIndex], numeros[vazioPos]] = [numeros[vazioPos], numeros[randomIndex]];
                    vazioPos = randomIndex;
                }
            }
            
            cronometro = setInterval(atualizarTempo, 1000);
            desenharTabuleiro();
        }
        
        function reiniciarJogoAtual() {
            if (cronometro) clearInterval(cronometro);
            iniciarJogo();
        }
        
        function desenharTabuleiro() {
            tabuleiroEl.innerHTML = '';
            numeros.forEach((num, indice) => {
                const bloco = document.createElement('div');
                bloco.className = 'tile';
                
                if (num === null) {
                    bloco.classList.add('empty');
                    bloco.innerHTML = '<i class="fa fa-circle-o" style="opacity:0.3; font-size:1rem;"></i>';
                } else {
                    bloco.textContent = num;
                    bloco.addEventListener('click', (e) => {
                        e.stopPropagation();
                        tentarMover(indice);
                    });
                    if (num === indice + 1) bloco.classList.add('correct');
                }
                tabuleiroEl.appendChild(bloco);
            });
        }
        
        function tentarMover(indice) {
            if (jogoFinalizado) return;
            
            const linhaIndice = Math.floor(indice / 4);
            const colunaIndice = indice % 4;
            const linhaVazio = Math.floor(vazioPos / 4);
            const colunaVazio = vazioPos % 4;
            
            const isAdjacente = (Math.abs(linhaIndice - linhaVazio) + Math.abs(colunaIndice - colunaVazio)) === 1;
            
            if (isAdjacente) {
                const blocoClicado = tabuleiroEl.children[indice];
                blocoClicado.classList.add('moving');
                
                setTimeout(() => {
                    [numeros[indice], numeros[vazioPos]] = [numeros[vazioPos], numeros[indice]];
                    vazioPos = indice;
                    movimentos++;
                    contadorEl.textContent = movimentos;
                    
                    desenharTabuleiro();
                    verificarVitoria();
                }, 100);
                
                setTimeout(() => {
                    if (blocoClicado) blocoClicado.classList.remove('moving');
                }, 150);
            }
        }
        
        function verificarVitoria() {
            let ganhou = true;
            for (let i = 0; i < 15; i++) {
                if (numeros[i] !== i + 1) {
                    ganhou = false;
                    break;
                }
            }
            
            if (ganhou && !jogoFinalizado) {
                jogoFinalizado = true;
                clearInterval(cronometro);
                
                // Cálculo de XP baseado em desempenho
                const xpBase = 50 + (nivel * 10);
                const bonusMovimentos = Math.max(0, Math.floor((80 - movimentos) / 5)) * 5;
                const xpTotal = xpBase + bonusMovimentos;
                xpGanhoNivel = xpTotal;
                
                finalMovimentosEl.textContent = movimentos;
                xpGanhoEl.textContent = xpTotal;
                mensagemVitoria.classList.remove('hidden');
                
                // Recorde
                if (recordeMovimentos === null || movimentos < recordeMovimentos) {
                    recordeMovimentos = movimentos;
                    localStorage.setItem('recordeMovimentos', recordeMovimentos);
                    atualizarInterfaceProgresso();
                    mostrarNotificacao('🏆 NOVO RECORDE! 🏆', 'success');
                }
                
                // Conceder XP
                adicionarXp(xpTotal);
                salvarProgresso();
                
                // Efeito de confete visual simples
                criarConfete();
            }
        }
        
        function criarConfete() {
            for (let i = 0; i < 30; i++) {
                const conf = document.createElement('div');
                conf.className = 'particle';
                conf.style.width = `${Math.random() * 8 + 4}px`;
                conf.style.height = conf.style.width;
                conf.style.left = `${Math.random() * 100}%`;
                conf.style.background = `hsl(${Math.random() * 360}, 70%, 60%)`;
                conf.style.animationDuration = `${Math.random() * 2 + 1}s`;
                conf.style.animationDelay = `${Math.random() * 0.5}s`;
                document.body.appendChild(conf);
                setTimeout(() => conf.remove(), 2000);
            }
        }
        
        function atualizarTempo() {
            if (!jogoFinalizado) {
                tempoSegundos++;
                const minutos = String(Math.floor(tempoSegundos / 60)).padStart(2, '0');
                const segundos = String(tempoSegundos % 60).padStart(2, '0');
                tempoEl.textContent = `${minutos}:${segundos}`;
            }
        }
        
        function proximoNivelHandler() {
            if (jogoFinalizado) {
                iniciarJogo();
                mensagemVitoria.classList.add('hidden');
                mostrarNotificacao(`Nível ${nivel} iniciado!`, 'primary');
            }
        }
        
        // Criar partículas de fundo
        function criarParticulas() {
            const container = document.getElementById('particles');
            for (let i = 0; i < 60; i++) {
                const particle = document.createElement('div');
                particle.className = 'particle';
                const size = Math.random() * 6 + 2;
                particle.style.width = `${size}px`;
                particle.style.height = `${size}px`;
                particle.style.left = `${Math.random() * 100}%`;
                particle.style.animationDuration = `${Math.random() * 20 + 10}s`;
                particle.style.animationDelay = `${Math.random() * 15}s`;
                particle.style.background = `rgba(99, 102, 241, ${Math.random() * 0.3})`;
                container.appendChild(particle);
            }
        }
        
        // Event Listeners
        botaoReiniciar.addEventListener('click', () => {
            if (cronometro) clearInterval(cronometro);
            iniciarJogo();
            mensagemVitoria.classList.add('hidden');
            mostrarNotificacao('Nível reiniciado', 'warning');
        });
        
        botaoResetTotal.addEventListener('click', resetarProgressoTotal);
        botaoProximo.addEventListener('click', proximoNivelHandler);
        
        // Inicialização
        carregarProgresso();
        iniciarJogo();
        criarParticulas();
        
        // Atualizar título com nível
        setInterval(() => {
            if (!jogoFinalizado && document.title !== `Nível ${nivel} | Paul Puzzle`) {
                document.title = `🎮 Nível ${nivel} | ${movimentos} movimentos`;
            }
        }, 500);
    </script>
</body>
</html>
