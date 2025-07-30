<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cowards Proxy Check</title>

    <script src="https://cdn.tailwindcss.com"></script>

    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Share+Tech+Mono&display=swap" rel="stylesheet">

    <style>
        body {
            font-family: 'Share Tech Mono', monospace;
            scrollbar-width: thin;
            scrollbar-color: #6D28D9 #111827;
        }
        body::-webkit-scrollbar {
            width: 8px;
        }
        body::-webkit-scrollbar-track {
            background: #111827;
        }
        body::-webkit-scrollbar-thumb {
            background-color: #6D28D9;
            border-radius: 20px;
        }
        .proxy-input::placeholder {
            color: #4B5563;
        }
    </style>
</head>
<body class="bg-gray-900 text-gray-200 min-h-screen flex flex-col items-center p-4 selection:bg-purple-500 selection:text-white">
    <div class="w-full max-w-4xl mx-auto">
        <header class="text-center my-8">
            <h1 class="text-5xl font-bold tracking-widest bg-gradient-to-r from-purple-500 via-pink-500 to-purple-500 bg-clip-text text-transparent animate-pulse">
                CowardsShop
            </h1>
            <p class="text-purple-400 mt-2 text-lg">Proxy Checker</p>
        </header>

        <main class="w-full">
            <div class="bg-gray-800/50 backdrop-blur-sm border border-purple-800/50 rounded-lg p-6 shadow-2xl shadow-purple-900/20">
                <label for="proxyInput" class="block text-sm font-medium text-gray-300 mb-2">Cole suas proxies (uma por linha)</label>
                <textarea id="proxyInput" rows="8" class="proxy-input w-full bg-gray-900 border-2 border-gray-700 rounded-md p-3 text-gray-200 focus:ring-2 focus:ring-purple-500 focus:border-purple-500 transition-all duration-300" placeholder="Ex: 192.168.1.1:8080\nuser:pass@127.0.0.1:3128"></textarea>

                <button id="checkButton" class="w-full mt-4 bg-purple-600 text-white font-bold py-3 px-4 rounded-lg hover:bg-purple-700 focus:outline-none focus:ring-4 focus:ring-purple-500/50 transform hover:scale-105 transition-all duration-300">
                    Verificar Proxies
                </button>
            </div>

            <div id="resultsContainer" class="mt-12 hidden">
                <h2 class="text-2xl font-bold text-center mb-6">Resultados da Verificação</h2>
                <div class="overflow-x-auto bg-gray-800/50 backdrop-blur-sm border border-purple-800/50 rounded-lg shadow-lg">
                    <table class="min-w-full text-sm text-left">
                        <thead class="bg-gray-900/70">
                            <tr>
                                <th scope="col" class="p-4 font-semibold text-purple-400 uppercase">Proxy</th>
                                <th scope="col" class="p-4 font-semibold text-purple-400 uppercase">Status</th>
                                <th scope="col" class="p-4 font-semibold text-purple-400 uppercase">País</th>
                                <th scope="col" class="p-4 font-semibold text-purple-400 uppercase">ISP</th>
                                <th scope="col" class="p-4 font-semibold text-purple-400 uppercase">Tipo</th>
                            </tr>
                        </thead>
                        <tbody id="resultsBody"></tbody>
                    </table>
                    <div id="loadingSpinner" class="hidden text-center p-8">
                        <svg class="animate-spin h-8 w-8 text-purple-500 mx-auto" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                            <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                            <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                        </svg>
                        <p class="mt-2">Verificando...</p>
                    </div>
                </div>
            </div>
        </main>

        <footer class="text-center text-gray-500 mt-12 mb-6">
            <p>&copy; 2025 CowardsShop. Todos os direitos reservados.</p>
        </footer>
    </div>

<script>
document.addEventListener('DOMContentLoaded', () => {
    const checkButton = document.getElementById('checkButton');
    const proxyInput = document.getElementById('proxyInput');
    const resultsContainer = document.getElementById('resultsContainer');
    const resultsBody = document.getElementById('resultsBody');
    const loadingSpinner = document.getElementById('loadingSpinner');

    checkButton.addEventListener('click', async () => {
        const proxies = proxyInput.value.trim().split('\n').filter(p => p.trim() !== '');

        if (proxies.length === 0) {
            alert('Por favor, insira pelo menos uma proxy para verificar.');
            return;
        }

        resultsBody.innerHTML = '';
        resultsContainer.classList.remove('hidden');
        loadingSpinner.classList.remove('hidden');

        for (const proxy of proxies) {
            const ip = proxy.includes('@') ? proxy.split('@')[1].split(':')[0] : proxy.split(':')[0];
            try {
                const response = await fetch(`https://proxycheck.io/v2/${ip}?key=i30r74-y40256-7890ta-1x54r2&vpn=1&asn=1`);
                const data = await response.json();

                if (data[ip]) {
                    const row = document.createElement('tr');
                    row.className = 'border-b border-gray-700/50 hover:bg-gray-800 transition-colors duration-200';

                    row.innerHTML = `
                        <td class="p-4 font-mono">${proxy}</td>
                        <td class="p-4 font-bold ${data[ip].proxy === 'yes' ? 'text-green-400' : 'text-red-500'}">${data[ip].proxy === 'yes' ? 'Funcionando' : 'Falha'}</td>
                        <td class="p-4">${data[ip].country || 'N/A'}</td>
                        <td class="p-4">${data[ip].isp || 'N/A'}</td>
                        <td class="p-4">${data[ip].type || 'N/A'}</td>
                    `;

                    resultsBody.appendChild(row);
                }
            } catch (error) {
                const row = document.createElement('tr');
                row.className = 'border-b border-gray-700/50 hover:bg-gray-800 transition-colors duration-200';
                row.innerHTML = `
                    <td class="p-4 font-mono">${proxy}</td>
                    <td class="p-4 font-bold text-red-500">Erro</td>
                    <td class="p-4">-</td>
                    <td class="p-4">-</td>
                    <td class="p-4">-</td>
                `;
                resultsBody.appendChild(row);
            }
        }

        loadingSpinner.classList.add('hidden');
    });
});
</script>
</body>
</html>
