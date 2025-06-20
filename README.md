<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Toolbox Credicel Unificado</title>
    
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Nunito:wght@400;600;700;800&display=swap" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/FileSaver.js/2.0.5/FileSaver.min.js"></script>

    <style>
        :root {
            --bg-color: #4c1d95;
            --card-bg: rgba(0, 0, 0, 0.2);
            --card-border-image: linear-gradient(to bottom right, rgba(255, 255, 255, 0.2), rgba(255, 255, 255, 0.05));
            --card-shadow: rgba(0, 0, 0, 0.5);
            --text-color: #FFFFFF;
            --text-color-darker: #E2E8F0;
            --control-bg: rgba(0, 0, 0, 0.25);
            --button-bg: rgba(255, 255, 255, 0.1);
            --button-border: rgba(255, 255, 255, 0.2);
            --input-bg: rgba(0, 0, 0, 0.3);
            --table-header-bg: rgba(0, 0, 0, 0.4);
            --table-row-border: rgba(255, 255, 255, 0.15);
            --table-row-even-bg: rgba(255, 255, 255, 0.03);
            --highlight-color: rgba(167, 139, 250, 0.3);
            --success-bg: rgba(57, 255, 20, 0.2);
            --danger-bg: rgba(248, 113, 113, 0.2);
            --info-bg: rgba(85, 107, 47, 0.2);
            --scroll-track-bg: rgba(255, 255, 255, 0.05);
            --scroll-thumb-bg: rgba(255, 255, 255, 0.2);
            --tab-active-bg: rgba(0, 0, 0, 0.1);
            --tab-inactive-bg: transparent;
        }

        body {
            font-family: 'Nunito', sans-serif;
            background-color: var(--bg-color); 
            color: var(--text-color);
        }
        
        .main-container {
            background: var(--card-bg);
            backdrop-filter: blur(20px) saturate(150%);
            -webkit-backdrop-filter: blur(20px) saturate(150%);
            border-radius: 1.5rem;
            box-shadow: 0 8px 32px 0 var(--card-shadow);
            position: relative;
            border: 1.5px solid transparent;
            background-clip: padding-box;
            z-index: 1;
        }
        .main-container::before {
            content: ''; position: absolute; inset: 0; border-radius: 1.5rem;
            padding: 1.5px; background: var(--card-border-image);
            -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
            -webkit-mask-composite: xor; mask-composite: exclude; pointer-events: none;
        }

        .input-field {
            background: var(--input-bg); border: 1px solid var(--button-border);
            border-radius: 0.75rem; padding: 0.75rem 1rem;
            color: var(--text-color); transition: background 0.3s, border-color 0.3s;
            width: 100%; box-sizing: border-box; resize: vertical;
        }
        .input-field::placeholder { color: var(--text-color-darker); opacity: 0.7; }
        .input-field:focus {
            outline: none; box-shadow: 0 0 0 2px var(--highlight-color); 
            border-color: var(--button-border); 
        }

        .btn { 
            color: var(--text-color); font-weight: 600; padding: 0.75rem 1.5rem; 
            border-radius: 999px; transition: all 0.2s ease-in-out;
            display: flex; align-items: center; justify-content: center;
            text-align: center; border: 1px solid var(--button-border);
            background: var(--button-bg); backdrop-filter: blur(10px);
            cursor: pointer;
        }
        .btn-small { padding: 0.3rem 0.8rem; font-size: 0.8rem; }
        .btn:hover:not(:disabled) {
            transform: translateY(-2px); box-shadow: 0 4px 15px rgba(0,0,0,0.2);
            background: rgba(255,255,255,0.15);
        }
        .btn:disabled { cursor: not-allowed; opacity: 0.5; }
        
        .summary-box {
            background: var(--control-bg); padding: 1rem; border-radius: 1rem; 
            border: 1px solid var(--button-border); 
        }
        .summary-box dt { font-weight: 500; opacity: 0.7; color: var(--text-color-darker); }
        .summary-box dd { margin-top: 0.25rem; font-weight: 600; }
        
        .table-cell { padding: 0.75rem; border-bottom: 1px solid var(--table-row-border); vertical-align: middle; } 
        .table-container { border-radius: 1rem; overflow: auto; }
        th.table-cell { 
            background-color: var(--table-header-bg); font-weight: 700;
            position: sticky; top: 0; backdrop-filter: blur(5px);
        }
        tbody tr:nth-child(even) { background-color: var(--table-row-even-bg); }
        td, th, h1, h2, h3, p, label { color: var(--text-color); }
        header p, header h1 { color: #FFFFFF !important;}

        .text-diff-red { color: #FCA5A5; }   
        .text-diff-green { color: #86EFAC; }
        .text-diff-blue { color: #93C5FD; }
        
        .tab-button {
            padding: 0.75rem 1.5rem;
            cursor: pointer;
            background-color: transparent;
            border: 1px solid transparent;
            border-bottom: none;
            transition: all 0.3s ease;
            font-weight: 700;
            opacity: 0.7;
            border-radius: 0.5rem 0.5rem 0 0;
            flex: 1; 
            display: flex;
            justify-content: center;
            align-items: center;
        }
        .tab-button:hover:not(.active) {
            background-color: rgba(255, 255, 255, 0.05);
            opacity: 1;
        }
        .tab-button.active {
            background-color: var(--tab-active-bg);
            border-color: var(--button-border);
            border-bottom-color: var(--tab-active-bg);
            opacity: 1;
        }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
        
        .modal {
            background-color: rgba(0,0,0,0.6);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
        }
        
        ::-webkit-scrollbar { width: 8px; height: 8px; }
        ::-webkit-scrollbar-track { background: var(--scroll-track-bg); border-radius: 10px; }
        ::-webkit-scrollbar-thumb { background: var(--scroll-thumb-bg); border-radius: 10px; }
        ::-webkit-scrollbar-thumb:hover { background: rgba(255, 255, 255, 0.4); }
        
        .data-list { list-style: none; padding: 0; }
        .data-list li { 
            padding: 1rem; 
            border-radius: 0.75rem; 
            background-color: rgba(0,0,0,0.2); 
            cursor: pointer;
            transition: background-color 0.2s;
            margin-bottom: 1rem;
        }
        .data-list li:hover { background-color: rgba(0,0,0,0.3); }

    </style>
</head>
<body class="p-4 md:p-6">
    <div class="main-container w-full max-w-screen-2xl mx-auto p-4 sm:p-6">
        <header class="mb-6 md:mb-8">
            <div class="flex justify-between items-center">
                 <div class="flex items-center space-x-4">
                    <i class="fas fa-toolbox text-3xl sm:text-4xl"></i>
                    <div>
                        <h1 class="text-2xl sm:text-3xl font-bold">Credicel</h1>
                        <p class="text-base sm:text-lg leading-tight opacity-80">Herramientas De Revision</p>
                        <p style="font-size: 5pt;">DLM</p>
                    </div>
                </div>
                 <div class="md:hidden">
                     <button id="mobile-menu-button" class="p-2 rounded-md text-white focus:outline-none focus:ring-2 focus:ring-inset focus:ring-white">
                         <i class="fas fa-bars text-2xl"></i>
                     </button>
                 </div>
            </div>

            <div class="border-b mt-4" style="border-color: var(--button-border);">
                <nav id="main-nav" class="hidden md:flex flex-col md:flex-row -mb-px space-y-2 md:space-y-0">
                    <button class="tab-button active" data-tab="activos"><i class="fas fa-calendar-check mr-2"></i>Activos</button>
                    <button class="tab-button" data-tab="liquidados"><i class="fas fa-calendar-alt mr-2"></i>Liquidados</button>
                    <button class="tab-button" data-tab="separador"><i class="fas fa-file-excel mr-2"></i>Separador</button>
                    <button class="tab-button" data-tab="tiendas"><i class="fas fa-store mr-2"></i>Tiendas</button>
                    <button class="tab-button" data-tab="analizador"><i class="fas fa-search-dollar mr-2"></i>Analizador</button>
                </nav>
            </div>
         </header>

        <div class="pt-6">
            <div id="activos" class="tab-content active">
                <div class="flex flex-col lg:flex-row lg:gap-6">
                    <div class="w-full lg:w-1/5 mb-6 lg:mb-0 space-y-4 flex flex-col">
                        <section class="summary-box">
                            <label for="structuredDataInput" class="block text-sm font-medium opacity-80 mb-2">Cuenta:</label>
                            <textarea id="structuredDataInput" class="input-field w-full text-xs" rows="4" placeholder="Pegar Datos De Cuenta..."></textarea>
                        </section>
                        <section class="summary-box">
                            <label for="rawData" class="block text-sm font-medium opacity-80 mb-2">Calendario De Cuenta:</label>
                            <textarea id="rawData" class="input-field w-full text-xs" rows="8" placeholder="Pegar Estado De Cuenta Aquí..."></textarea>
                        </section>
                        <div class="space-y-3">
                            <button onclick="window.processAllDataActivos()" class="btn w-full text-sm sm:text-base"><span>Analizar Datos</span> <i class="fas fa-cogs ml-2"></i></button>
                            <button id="saveAndClearBtnActivos" onclick="window.saveAndClearActivos()" class="btn w-full text-sm sm:text-base hidden"><span>Archivar y Limpiar</span> <i class="fas fa-archive ml-2"></i></button>
                            <button id="captureButtonActivos" class="btn w-full text-sm sm:text-base hidden"><span>Captura</span> <i class="fas fa-camera ml-2"></i></button>
                        </div>
                        <section id="historySectionActivos" class="summary-box flex-grow flex flex-col">
                            <div class="flex justify-between items-center mb-3 pb-2 border-b" style="border-color: var(--table-row-border);">
                                <h2 class="font-semibold text-center">Historial Archivado</h2>
                                <button id="viewHistorySummaryBtnActivos" onclick="window.openHistorySummaryActivos()" class="btn btn-small hidden"><i class="fas fa-table mr-1"></i>Ver</button>
                            </div>
                            <div class="overflow-auto table-container flex-grow">
                                <table class="min-w-full">
                                    <thead><tr><th class="table-cell text-left text-xs">TAG</th><th class="table-cell text-left text-xs">Cliente</th><th class="table-cell text-left text-xs">Balance</th><th class="table-cell text-left text-xs">Accion</th></tr></thead>
                                    <tbody id="historyTableBodyActivos" class="text-xs sm:text-sm"></tbody>
                                </table>
                            </div>
                        </section>
                    </div>
                    <div class="w-full lg:w-4/5 space-y-4">
                        <div id="errorMessageActivos" class="mb-4 p-3 rounded-lg hidden text-xs sm:text-sm" style="background-color: var(--danger-bg);"></div>
                        <section id="resultsSectionActivos" class="hidden space-y-4">
                            <div id="captureTargetActivos">
                                <div id="summaryActivos" class="summary-box"></div>
                                <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mt-4">
                                   <div id="weeksSummaryActivos" class="summary-box"></div>
                                   <div id="amountSummaryActivos" class="summary-box"></div>
                                </div>
                                <div id="rebuyCandidateSectionActivos" class="hidden status-box rebuy-candidate-box p-3 rounded-lg text-center font-semibold mt-4" style="background-color: var(--success-bg);">Candidato a Recompra</div>
                                <div id="referCandidateSectionActivos" class="hidden status-box refer-candidate-box p-3 rounded-lg text-center font-semibold mt-4" style="background-color: var(--info-bg);">Ya Puede Referir</div>
                                <div id="paymentDetailsContainerActivos" class="summary-box mt-4">
                                    <h2 class="font-semibold text-center mb-3 pb-2 border-b" style="border-color: var(--table-row-border);">Calendario de Pagos</h2>
                                    <div class="overflow-auto table-container">
                                        <table class="min-w-full">
                                            <thead><tr><th class="table-cell text-left text-xs font-medium uppercase tracking-wider">Etiqueta</th><th class="table-cell text-left text-xs font-medium uppercase tracking-wider">Estado</th><th class="table-cell text-left text-xs font-medium uppercase tracking-wider">Monto</th><th class="table-cell text-left text-xs font-medium uppercase tracking-wider">Pagado En</th><th class="table-cell text-left text-xs font-medium uppercase tracking-wider">Fecha de Pago</th><th class="table-cell text-left text-xs font-medium uppercase tracking-wider">Fecha Limite</th><th class="table-cell text-left text-xs font-medium uppercase tracking-wider">Fecha Original</th><th class="table-cell text-left text-xs font-medium uppercase tracking-wider">Dif. Fechas RECALCULADO</th><th class="table-cell text-left text-xs font-medium uppercase tracking-wider">Dif. Pago ORIGINAL</th></tr></thead>
                                            <tbody id="paymentTableBodyActivos" class="text-xs sm:text-sm"></tbody>
                                        </table>
                                    </div>
                                </div>
                            </div>
                        </section>
                    </div>
                </div>
            </div>

            <div id="liquidados" class="tab-content">
                <div class="flex flex-col lg:flex-row lg:gap-6">
                    <div class="w-full lg:w-1/5 mb-6 lg:mb-0 space-y-4 flex flex-col">
                        <section class="summary-box">
                            <label for="clientDataLiquidados" class="block text-sm font-medium opacity-80 mb-2">Cuenta:</label>
                            <textarea id="clientDataLiquidados" class="input-field text-xs" rows="4" placeholder="Pegar Datos De Cuenta..."></textarea>
                        </section>
                        <section class="summary-box">
                            <label for="inputTextLiquidados" class="block text-sm font-medium opacity-80 mb-2">Calendario De Cuenta:</label>
                            <textarea id="inputTextLiquidados" class="input-field text-xs" rows="8" placeholder="Pegar Estado De Cuenta Aquí..."></textarea>
                        </section>
                        <div class="space-y-3">
                            <button id="processButtonLiquidados" class="btn text-sm sm:text-base w-full"><span>Procesar</span> <i class="fas fa-cogs ml-2"></i></button>
                            <button id="archiveButtonLiquidados" class="btn text-sm sm:text-base w-full hidden"><span>Archivar y Limpiar</span><i class="fas fa-archive ml-2"></i></button>
                            <button id="captureButtonLiquidados" class="btn w-full text-sm sm:text-base hidden"><span>Generar Captura</span> <i class="fas fa-camera ml-2"></i></button>
                        </div>
                        <section id="historySectionLiquidados" class="summary-box mt-4 flex-grow flex flex-col">
                            <div class="flex justify-between items-center mb-3 pb-2 border-b" style="border-color: var(--table-row-border);">
                                <h2 class="font-semibold text-center">Historial</h2>
                                <button id="viewHistoryBtnLiquidados" class="btn btn-small hidden"><i class="fas fa-table mr-1"></i>Ver Resumen</button>
                            </div>
                            <div class="overflow-auto table-container flex-grow">
                                <table class="min-w-full">
                                    <thead><tr><th class="table-cell text-left text-xs">TAG</th><th class="table-cell text-left text-xs">Cliente</th><th class="table-cell text-left text-xs">Balance</th><th class="table-cell text-left text-xs">Accion</th></tr></thead>
                                    <tbody id="historyTableBodyLiquidados" class="text-xs"></tbody>
                                </table>
                            </div>
                        </section>
                    </div>
                    <div class="w-full lg:w-4/5 space-y-4 flex flex-col">
                        <div id="errorMessageLiquidados" class="p-3 rounded-lg hidden text-xs sm:text-sm" style="background-color: var(--danger-bg);"></div>
                        <section id="resultsContentLiquidados" class="hidden space-y-4 flex flex-col flex-grow">
                           <div id="captureTargetLiquidados">
                                <div id="summarySectionLiquidados" class="summary-box"></div>
                                <div id="resultsTableContainerLiquidados" class="summary-box flex-grow flex flex-col mt-4">
                                    <h2 class="font-semibold text-center mb-3 pb-2 border-b" style="border-color: var(--table-row-border);">Detalles de Pagos</h2>
                                    <div class="overflow-auto table-container flex-grow">
                                        <table class="min-w-full">
                                            <thead><tr><th class="table-cell text-left text-xs font-medium uppercase tracking-wider">Etiqueta</th><th class="table-cell text-left text-xs font-medium uppercase tracking-wider">Fecha Limite</th><th class="table-cell text-left text-xs font-medium uppercase tracking-wider">Monto</th><th class="table-cell text-left text-xs font-medium uppercase tracking-wider">Estado</th><th class="table-cell text-left text-xs font-medium uppercase tracking-wider">Pagado el Dia</th><th class="table-cell text-left text-xs font-medium uppercase tracking-wider">Diferencia</th></tr></thead>
                                            <tbody id="paymentTableBodyLiquidados" class="text-xs sm:text-sm"></tbody>
                                        </table>
                                    </div>
                                </div>
                            </div>
                        </section>
                    </div>
                </div>
            </div>

            <div id="separador" class="tab-content">
                 <main class="text-center">
                    <p class="opacity-80 mb-6">Selecciona tu archivo .xlsx. El sistema generara un archivo ZIP con un <strong>archivo Excel (.xlsx)</strong> para cada grupo encontrado en la columna "Dat's".</p>
                    <div class="flex flex-col items-center space-y-4 max-w-md mx-auto">
                        <label for="excelFileInputSeparador" class="btn w-full cursor-pointer">
                            <i class="fas fa-file-upload mr-2"></i>
                            <span id="excelFileSeparadorLabel">Seleccionar Archivo .xlsx</span>
                        </label>
                        <input type="file" id="excelFileInputSeparador" accept=".xlsx" class="hidden">
                        <button id="processButtonSeparador" disabled class="btn w-full">
                            <i class="fas fa-cogs"></i>
                            <span class="ml-2">Procesar y Descargar ZIP</span>
                        </button>
                    </div>
                    <p id="messageSeparador" class="mt-6 font-semibold p-3 rounded-lg"></p>
                    <div id="summaryContainerSeparador" class="mt-6 max-w-md mx-auto hidden">
                        <h3 class="text-xl font-semibold mb-4 text-center">Resumen de Separacion</h3>
                        <div class="summary-box">
                            <div class="table-container">
                               <table class="min-w-full">
                                    <thead>
                                        <tr>
                                            <th class="table-cell text-left">Grupo (Dat's)</th>
                                            <th class="table-cell text-right">Numero de Cuentas</th>
                                        </tr>
                                    </thead>
                                    <tbody id="summaryTableBodySeparador">
                                    </tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                </main>
            </div>

            <div id="tiendas" class="tab-content">
                <main class="space-y-6">
                    <section class="summary-box">
                        <h3 class="text-xl font-semibold mb-4 text-center">Mapa de Sucursales</h3>
                        <div id="currentSucursalLegendTiendas" class="bg-black/30 p-2 rounded-lg mb-4 text-sm text-center hidden"></div>
                        <iframe id="map-iframe-tiendas" title="Mapa de Sucursales" src="about:blank" loading="lazy" allowfullscreen class="w-full h-96 lg:h-[500px] rounded-lg border-0"></iframe>
                    </section>
            
                    <div class="flex flex-col lg:flex-row gap-6">
                        <div class="w-full lg:w-1/5 flex flex-col gap-6">
                            <section class="summary-box space-y-4">
                                <h3 class="text-xl font-semibold text-center">Busqueda y Filtros</h3>
                                <div>
                                    <label for="companySelectTiendas" class="block text-sm font-medium mb-1">Por Empresa:</label>
                                    <select id="companySelectTiendas" class="input-field w-full text-sm"></select>
                                </div>
                                <div>
                                    <label for="stateSearchTiendas" class="block text-sm font-medium">Por Estado:</label>
                                    <input type="text" id="stateSearchTiendas" list="stateDatalistTiendas" placeholder="Ej: 'Puebla'" class="input-field w-full text-sm">
                                    <datalist id="stateDatalistTiendas"></datalist>
                                </div>
                                <div>
                                    <label for="municipalitySearchTiendas" class="block text-sm font-medium">Por Municipio:</label>
                                    <input type="text" id="municipalitySearchTiendas" list="municipalityDatalistTiendas" placeholder="Ej: 'Puebla'" class="input-field w-full text-sm">
                                    <datalist id="municipalityDatalistTiendas"></datalist>
                                </div>
                                <div class="grid grid-cols-2 gap-3">
                                  <button id="searchLocationButtonTiendas" class="btn text-sm"><i class="fas fa-map-marker-alt"></i>Buscar</button>
                                  <button id="clearFiltersButtonTiendas" class="btn text-sm" style="background-color: var(--danger-bg);"><i class="fas fa-times"></i>Limpiar</button>
                                </div>
                            </section>
                
                            <section class="summary-box space-y-4">
                                <h3 class="text-xl font-semibold text-center">Sucursales Cercanas por C.P.</h3>
                                 <div>
                                     <label for="postalCodeSearchTiendas" class="block text-sm font-medium">Ingresa un Codigo Postal:</label>
                                     <input type="text" id="postalCodeSearchTiendas" placeholder="Ej: 97246" class="input-field w-full text-sm text-center" maxlength="5" oninput="this.value = this.value.replace(/[^0-9]/g, '')">
                                </div>
                                <button id="searchNearestButtonTiendas" disabled class="btn text-sm w-full"><i class="fas fa-street-view"></i>Buscar 10 Mas Cercanas</button>
                                <div id="postalCodeSearchInfoTiendas" class="text-sm text-center mt-2 hidden"></div>
                                <ul id="nearestBranchesListTiendas" class="data-list mt-4 text-sm"><li class="opacity-70 text-center">Realiza una busqueda por C.P. para ver resultados.</li></ul>
                            </section>
                        </div>
            
                        <section class="summary-box space-y-4 w-full lg:w-4/5">
                           <h3 class="text-xl font-semibold text-center">Listado de Sucursales</h3>
                            <div class="mb-2">
                                <label for="branchNameSearchTiendas" class="block text-sm font-medium">Buscar por Sucursal:</label>
                                 <input type="text" id="branchNameSearchTiendas" placeholder="Nombre de la sucursal..." class="input-field w-full text-sm">
                            </div>
                            <div id="sucursalGridContainer" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 xl:grid-cols-6 gap-4 overflow-y-auto max-h-[60vh] p-2">
                            </div>
                        </section>
                    </div>
            
                    <section class="summary-box">
                        <h2 class="text-xl font-semibold mb-4 text-center">Cargar Archivos</h2>
                         <div class="flex flex-col md:flex-row justify-center items-center gap-6 mb-4">
                            <div class="flex flex-col items-center space-y-2 w-full md:w-auto">
                                <label class="font-medium text-sm text-center"><i class="fas fa-file-excel mr-2"></i>1. Excel de Sucursales (.xlsx)</label>
                                <label for="excelFileTiendas" class="btn text-sm w-full cursor-pointer justify-center">
                                    <i class="fas fa-upload mr-2"></i>
                                    <span id="excelFileTiendasLabel">Seleccionar Archivo</span>
                                </label>
                                <input type="file" id="excelFileTiendas" accept=".xlsx" class="hidden">
                            </div>
                            <div class="flex flex-col items-center space-y-2 w-full md:w-auto">
                                <label class="font-medium text-sm text-center"><i class="fas fa-file-code mr-2"></i>2. JSON de Coordenadas</label>
                                <label for="jsonFileTiendas" class="btn text-sm w-full cursor-pointer justify-center">
                                    <i class="fas fa-upload mr-2"></i>
                                    <span id="jsonFileTiendasLabel">Seleccionar Archivo</span>
                                </label>
                                <input type="file" id="jsonFileTiendas" accept=".json" class="hidden">
                            </div>
                        </div>
                        <div class="flex flex-col md:flex-row justify-center items-center gap-6">
                            <button id="loadDataButtonTiendas" class="btn text-sm w-full md:w-auto" disabled><i class="fas fa-cogs mr-2"></i>Cargar Excel</button>
                            <button id="loadJsonButtonTiendas" class="btn text-sm w-full md:w-auto" disabled><i class="fas fa-cogs mr-2"></i>Cargar JSON</button>
                        </div>
                         <div id="progressBarContainerTiendas" class="w-full bg-black/20 rounded-full mt-4 hidden">
                            <div id="progressBarTiendas" class="bg-gradient-to-r from-purple-500 to-pink-500 text-xs font-medium text-center p-0.5 leading-none rounded-full" style="width: 0%">0%</div>
                        </div>
                        <div id="loadingMessageTiendas" class="text-sm text-center mt-2"></div>
                        <div id="jsonLoadingMessageTiendas" class="text-sm text-center mt-2"></div>
                        <div id="errorMessageTiendas" class="text-sm text-center mt-2 hidden" style="color: var(--danger-bg);"></div>
                        <div id="infoMessageTiendas" class="text-sm text-center mt-2 hidden" style="color: var(--highlight-color);"></div>
                    </section>
                </main>
            </div>
            
            <div id="analizador" class="tab-content">
                <div class="flex flex-col lg:flex-row lg:gap-6 h-[80vh]">
                     <div class="lg:w-1/5 space-y-4 p-4 summary-box overflow-y-auto">
                        <h2 class="text-xl font-bold text-center">Analizador de Datos</h2>
                        
                        <div class="space-y-2">
                            <label for="file-input-analyzer" class="btn w-full cursor-pointer">
                                <i class="fas fa-file-upload mr-2"></i>
                                <span id="file-info-analyzer">Seleccionar Archivo</span>
                            </label>
                            <input type="file" id="file-input-analyzer" accept=".xlsx, .xls, .csv" class="hidden">
                        </div>

                        <div id="progress-container-analyzer" class="w-full bg-gray-700 rounded-full my-2 hidden">
                            <div id="progress-bar-analyzer" class="bg-purple-500 text-xs font-medium text-white text-center p-0.5 leading-none rounded-full" style="width: 0%">0%</div>
                        </div>
                        
                        <div class="space-y-2">
                             <label for="search-column-analyzer" class="block text-sm font-medium">Buscar en:</label>
                             <select id="search-column-analyzer" class="input-field" disabled>
                                 <option value="all">Todas las columnas</option>
                             </select>
                        </div>

                        <div class="space-y-2">
                            <label for="search-input-analyzer" class="block text-sm font-medium">Termino de Busqueda:</label>
                            <input type="text" id="search-input-analyzer" placeholder="Escribe tu busqueda aqui..." class="input-field" disabled>
                        </div>
                        
                        <div class="space-y-3">
                            <button id="search-specific-button-analyzer" class="btn w-full" disabled>Buscar Datos</button>
                            <button id="search-detail-button-analyzer" class="btn w-full" disabled>Buscar Detalles De Venta</button>
                        </div>

                        <div class="space-y-3 mt-4">
                            <button id="download-button-analyzer" class="btn w-full" disabled>Descargar Resultados</button>
                        </div>
                    </div>

                    <div class="lg:w-4/5 space-y-4 p-4 summary-box flex flex-col">
                         <div id="status-analyzer" class="text-center font-semibold"></div>
                         <div class="table-container flex-grow">
                            <table class="min-w-full" id="results-table-analyzer">
                                <thead id="table-headers-analyzer"></thead>
                                <tbody id="table-body-analyzer"></tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </div>

        </div>
    </div>

    <div id="historySummaryModalActivos" class="fixed inset-0 z-50 hidden items-center justify-center modal">
        <div class="main-container w-11/12 max-w-7xl mx-auto p-6 relative">
             <div class="flex justify-between items-center mb-4">
                <h2 class="text-xl font-bold">Resumen de Historial (Activos)</h2>
                <div class="flex items-center gap-2">
                    <button onclick="window.downloadHistorySummaryActivos()" class="btn btn-small" title="Descargar"><i class="fas fa-download"></i></button>
                    <button onclick="window.closeHistorySummaryActivos()" class="btn btn-small" title="Cerrar"><i class="fas fa-times"></i></button>
                </div>
            </div>
            <div class="overflow-auto table-container" style="max-height: 80vh;">
                <table class="min-w-full text-xs">
                    <thead><tr><th class="table-cell">TAG</th><th class="table-cell">Nombre</th><th class="table-cell">FOLIO</th><th class="table-cell">Fecha Compra</th><th class="table-cell">Fecha Liquidacion</th><th class="table-cell">Inversion</th><th class="table-cell">Version INEQ</th><th class="table-cell">Marca y modelo</th><th class="table-cell">Progreso Cubierto</th><th class="table-cell">Balance vs. Fecha Limite</th><th class="table-cell">Balance vs. Fecha Original</th></tr></thead>
                    <tbody id="historySummaryTableBodyActivos"></tbody>
                </table>
            </div>
        </div>
    </div>

    <div id="historyModalLiquidados" class="fixed inset-0 z-50 hidden items-center justify-center modal">
        <div class="main-container w-11/12 max-w-7xl mx-auto p-4 sm:p-6 flex flex-col max-h-[90vh]">
            <header class="mb-4 flex justify-between items-center">
                <h2 class="text-xl font-bold">Resumen de Historial (Liquidados)</h2>
                 <div class="flex items-center gap-2">
                    <button onclick="window.downloadHistorySummaryLiquidados()" class="btn btn-small" title="Descargar"><i class="fas fa-download"></i></button>
                    <button id="closeHistoryModalLiquidados" class="btn btn-small"><i class="fas fa-times"></i></button>
                </div>
            </header>
            <div class="overflow-auto table-container flex-grow">
                <table class="min-w-full text-xs">
                    <thead><tr><th class="table-cell">TAG</th><th class="table-cell">Nombre</th><th class="table-cell">Folio</th><th class="table-cell">IMEI</th><th class="table-cell">F. Compra</th><th class="table-cell">F. Liquid.</th><th class="table-cell">Semanas</th><th class="table-cell">Ult. Pago</th><th class="table-cell">Dif. Liquid.</th></tr></thead>
                    <tbody id="historySummaryTableBodyLiquidados"></tbody>
                </table>
            </div>
        </div>
    </div>
    
<script>
// Se ejecuta cuando el contenido del DOM ha sido completamente cargado.
// Este es el punto de entrada principal para toda la lógica de JavaScript.
document.addEventListener('DOMContentLoaded', () => {
    // --- LÓGICA DEL MENÚ MÓVIL ---
    const mobileMenuButton = document.getElementById('mobile-menu-button');
    const mainNav = document.getElementById('main-nav');

    // Asigna el evento 'click' al botón del menú hamburguesa.
    // Al hacer clic, alterna la clase 'hidden' para mostrar u ocultar la navegación en dispositivos móviles.
    if (mobileMenuButton && mainNav) {
        mobileMenuButton.addEventListener('click', () => {
            mainNav.classList.toggle('hidden');
        });
    }

    // --- CONTROLES GLOBALES DE PESTAÑAS ---
    const tabButtons = document.querySelectorAll('.tab-button');
    const tabContents = document.querySelectorAll('.tab-content');

    // Itera sobre cada botón de pestaña para asignarle su funcionalidad.
    tabButtons.forEach(button => {
        button.addEventListener('click', () => {
            // Primero, desactiva todas las pestañas y contenidos.
            tabButtons.forEach(btn => btn.classList.remove('active'));
            tabContents.forEach(content => content.classList.remove('active'));
            
            // Luego, activa solo la pestaña en la que se hizo clic y su contenido correspondiente.
            // Utiliza el atributo `data-tab` del botón para encontrar el ID del contenido a mostrar.
            button.classList.add('active');
            document.getElementById(button.dataset.tab).classList.add('active');

            // Cierra el menú móvil si está abierto después de seleccionar una opción.
            if (!mainNav.classList.contains('md:flex')) {
                mainNav.classList.add('hidden');
            }
        });
    });

    // --- FUNCIONES DE UTILIDAD (Disponibles para todo el script) ---

    /**
     * Devuelve un valor de texto o un valor predeterminado si el original está vacío, nulo o indefinido.
     * @param {*} value - El valor a verificar.
     * @param {string} [defaultValue='---'] - El valor a devolver si el original está vacío.
     * @returns {string} El valor original como texto o el valor predeterminado.
     */
    function getText(value, defaultValue = '---') {
        return (value === null || typeof value === 'undefined' || String(value).trim() === '') ? defaultValue : String(value);
    }

    /**
     * Formatea una cadena de fecha en formato 'YYYY-MM-DD' a un formato más legible 'DD/Mes/AAAA' (ej. 25/Dic/2023).
     * @param {string} dateString - La fecha en formato 'YYYY-MM-DD'.
     * @returns {string} La fecha formateada o la original si ocurre un error.
     */
    function formatDateDDMMMAAAA(dateString) {
        if (!dateString || dateString === 'N/A') return 'N/A';
        try {
            const datePart = dateString.split(' ')[0];
            const parts = datePart.split('-');
            if (parts.length !== 3 || parts.some(part => isNaN(parseInt(part)))) return dateString;
            const [year, month, day] = parts.map(p => parseInt(p, 10));
            if (isNaN(year) || isNaN(month) || isNaN(day) || month < 1 || month > 12 || day < 1 || day > 31) return dateString;
            const monthNames = ['Ene', 'Feb', 'Mar', 'Abr', 'May', 'Jun', 'Jul', 'Ago', 'Sep', 'Oct', 'Nov', 'Dic'];
            return `${String(day).padStart(2, '0')}/${monthNames[month - 1]}/${year}`;
        } catch (error) {
            console.error("Error formatting date:", dateString, error);
            return dateString;
        }
    }
    
    /**
     * Oculta un elemento HTML añadiendo la clase 'hidden'.
     * @param {HTMLElement} element - El elemento a ocultar.
     */
    function hide(element) { if(element) element.classList.add('hidden'); }
    
    /**
     * Muestra un elemento HTML quitando la clase 'hidden'.
     * @param {HTMLElement} element - El elemento a mostrar.
     */
    function show(element) { if(element) element.classList.remove('hidden'); }

    // --- EXPOSICIÓN GLOBAL DE FUNCIONES ---
    // Estas funciones se asignan al objeto `window` para que puedan ser llamadas
    // directamente desde los atributos `onclick` en el HTML. Esto conecta el HTML con el JS.
    window.showHistoryDetailsActivos = (index) => {
        const scriptScope = document.getElementById('activos')._scriptScope;
        if(scriptScope) scriptScope.showHistoryDetails(index);
    };
    window.openHistorySummaryActivos = () => {
        const scriptScope = document.getElementById('activos')._scriptScope;
        if(scriptScope) scriptScope.openHistorySummary();
    };
    window.closeHistorySummaryActivos = () => {
         const scriptScope = document.getElementById('activos')._scriptScope;
        if(scriptScope) scriptScope.closeHistorySummary();
    }
    window.downloadHistorySummaryActivos = () => {
         const scriptScope = document.getElementById('activos')._scriptScope;
        if(scriptScope) scriptScope.downloadHistorySummary();
    }
    window.processAllDataActivos = () => {
        const scriptScope = document.getElementById('activos')._scriptScope;
        if(scriptScope) scriptScope.processAllData();
    }
     window.saveAndClearActivos = () => {
        const scriptScope = document.getElementById('activos')._scriptScope;
        if(scriptScope) scriptScope.saveAndClear();
    }
    window.showSucursalOnMap = function (id_excel) {
        if(window.tiendasScript && window.tiendasScript.showSucursalOnMap) {
            window.tiendasScript.showSucursalOnMap(id_excel);
        }
    }
    window.copySucursalData = function(sucursalId, buttonElement) {
        if(window.tiendasScript && window.tiendasScript.copySucursalData) {
            window.tiendasScript.copySucursalData(sucursalId, buttonElement);
        }
    }

    // --- SCRIPT PARA LA PESTAÑA DE CALENDARIO DE ACTIVOS ---
    // El código para esta pestaña está encapsulado en una IIFE (Immediately Invoked Function Expression)
    // para evitar que sus variables y funciones contaminen el ámbito global.
    ;(() => {
        // Objeto `scope` para mantener el estado y funciones de esta pestaña.
        const scope = {};
        document.getElementById('activos')._scriptScope = scope;

        let savedSummaries = [];
        let currentProcessedData = null;

        // Mapeo de elementos de la UI para un acceso más fácil y limpio.
        const ui = {
            structuredDataInput: document.getElementById('structuredDataInput'),
            rawData: document.getElementById('rawData'),
            saveAndClearBtn: document.getElementById('saveAndClearBtnActivos'),
            captureButton: document.getElementById('captureButtonActivos'),
            historySection: document.getElementById('historySectionActivos'),
            viewHistorySummaryBtn: document.getElementById('viewHistorySummaryBtnActivos'),
            historyTableBody: document.getElementById('historyTableBodyActivos'),
            errorMessage: document.getElementById('errorMessageActivos'),
            resultsSection: document.getElementById('resultsSectionActivos'),
            summary: document.getElementById('summaryActivos'),
            weeksSummary: document.getElementById('weeksSummaryActivos'),
            amountSummary: document.getElementById('amountSummaryActivos'),
            rebuyCandidateSection: document.getElementById('rebuyCandidateSectionActivos'),
            referCandidateSection: document.getElementById('referCandidateSectionActivos'),
            paymentDetailsContainer: document.getElementById('paymentDetailsContainerActivos'),
            paymentTableBody: document.getElementById('paymentTableBodyActivos'),
            historySummaryModal: document.getElementById('historySummaryModalActivos'),
            historySummaryTableBody: document.getElementById('historySummaryTableBodyActivos'),
            captureTarget: document.getElementById('captureTargetActivos'),
        };
        
        /**
         * Convierte una fecha en formato 'DD/Mes/AAAA' a un objeto Date.
         * @param {string} dateStr - La fecha a convertir.
         * @returns {Date|null} El objeto Date o null si el formato es inválido.
         */
        function parseDateFromDDMMMAAAA(dateStr) {
            if (!dateStr || dateStr === 'N/A') return null;
            const monthNames = {'Ene':0, 'Feb':1, 'Mar':2, 'Abr':3, 'May':4, 'Jun':5, 'Jul':6, 'Ago':7, 'Sep':8, 'Oct':9, 'Nov':10, 'Dic':11};
            const parts = dateStr.split('/');
            if (parts.length !== 3) return null;
            const day = parseInt(parts[0], 10);
            const month = monthNames[parts[1]];
            const year = parseInt(parts[2], 10);
            if (isNaN(day) || month === undefined || isNaN(year)) return null;
            return new Date(Date.UTC(year, month, day));
        }

        /**
         * Calcula la diferencia en días entre dos fechas.
         * @param {string} startDateStr - La fecha de inicio ('YYYY-MM-DD').
         * @param {string} endDateStr - La fecha de fin ('YYYY-MM-DD').
         * @returns {number|string} La diferencia en días o 'N/A' si hay error.
         */
        function calculateDateDifferenceInDays(startDateStr, endDateStr) {
            if (!startDateStr || startDateStr === 'N/A' || !endDateStr || endDateStr === 'N/A') return 'N/A';
            try {
                const startDate = new Date(startDateStr.split(' ')[0]);
                const endDate = new Date(endDateStr.split(' ')[0]);
                if (isNaN(startDate.getTime()) || isNaN(endDate.getTime())) return 'N/A';
                return Math.round((endDate - startDate) / (1000 * 60 * 60 * 24));
            } catch (e) {
                console.error("Error calculando la diferencia de fechas:", e);
                return 'N/A';
            }
        }

        /**
         * Parsea el texto del estado de cuenta para extraer datos de cabecera y una lista de pagos.
         * Esta es una de las funciones más críticas y depende del formato exacto del texto de entrada.
         * @param {string} text - El texto del estado de cuenta a analizar.
         * @param {object} existingHeader - Datos de cabecera preexistentes del otro campo de texto.
         * @returns {object} Un objeto con los datos de cabecera, la lista de pagos y posibles errores.
         */
        function parseCalendarData(text, existingHeader) {
            const lines = text.replace(/\r\n|\r/g, '\n').split('\n').map(line => line.trim());
            const data = { header: {...existingHeader}, payments: [], errors: [] };
            
            const headerKeys = {
                "Estado de cuenta TAG:": "tag", "Saldo para liquidar:": "saldoLiquidar",
                "Sumatoria pagos recibidos:": "pagosRecibidos", "Bonificaciones:": "bonificaciones",
                "Monedero electronico ocupado:": "monederoOcupado"
            };

            let paymentStartIndex = -1;
            for (let i = 0; i < lines.length; i++) {
                const line = lines[i];
                if (!line) continue;
                if (line.startsWith("Fecha Pago:")) { paymentStartIndex = i; break; }
                Object.keys(headerKeys).forEach(key => {
                    if (line.startsWith(key)) data.header[headerKeys[key]] = line.substring(key.length).trim();
                });
            }

            if (paymentStartIndex === -1 && text.trim() !== '') {
                const dateRegexForStart = /(\d{4}-\d{2}-\d{2})/;
                const firstDateIndex = lines.findIndex(l => dateRegexForStart.test(l));
                if (firstDateIndex > -1) paymentStartIndex = firstDateIndex;
                else return data;
            }
            
            const paymentTextBlob = paymentStartIndex !== -1 ? "Fecha Pago:\n" + lines.slice(paymentStartIndex).join('\n') : "";
            const paymentSections = paymentTextBlob.split("Fecha Pago:").filter(section => section.trim() !== "");
            
            let lastPaidDate = null;
            let lastOriginalDateObj = null;
            let lastValidFechaRealPago = null;
            let firstVencidoFound = false;

            paymentSections.forEach((section, idx) => {
                const paymentLines = section.trim().split('\n').map(l => l.trim()).filter(Boolean);
                const payment = { id: `payment-${idx}`, isEnganche: (idx === 0) };
                if (paymentLines.length === 0) return;

                const dateRegex = /(\d{4}-\d{2}-\d{2})/;
                let dateLineIndex = paymentLines.findIndex(l => dateRegex.test(l));
                payment.originalFechaLimitePago = (dateLineIndex > -1) ? paymentLines[dateLineIndex].match(dateRegex)[0] : "N/A";
                payment.fechaLimitePago = formatDateDDMMMAAAA(payment.originalFechaLimitePago);
                payment.etiqueta = payment.isEnganche ? "Enganche" : `Semana<br>${String(idx).padStart(2, '0')}`;
                
                const dateParts = payment.originalFechaLimitePago.split('-');
                if (dateParts.length === 3 && !dateParts.some(isNaN)) {
                    const [year, month, day] = dateParts.map(p => parseInt(p, 10));
                    if (payment.isEnganche) lastOriginalDateObj = new Date(Date.UTC(year, month - 1, day));
                    else if (lastOriginalDateObj) lastOriginalDateObj.setUTCDate(lastOriginalDateObj.getUTCDate() + 7);
                }
                payment.originalFechaOriginalCalculada = lastOriginalDateObj ? lastOriginalDateObj.toISOString().split('T')[0] : "N/A";
                payment.fechaOriginalCalculada = formatDateDDMMMAAAA(payment.originalFechaOriginalCalculada);
                payment.montoDetectado = "N/A";
                payment.originalFechaRealPago = "N/A";
                payment.pagadoEn = "N/A";
                payment.status = "N/A";

                for (let k = 0; k < paymentLines.length; k++) {
                    const line = paymentLines[k];
                    if (line.includes(payment.originalFechaLimitePago)) continue;
                    const amountRegex = /^\$?\s*[\d,]+(\.\d{2})?$/;
                    if (line.startsWith("Monto:")) {
                        let amount = line.substring("Monto:".length).trim();
                        if (amount) payment.montoDetectado = amount;
                        else if (k + 1 < paymentLines.length && amountRegex.test(paymentLines[k + 1])) { payment.montoDetectado = paymentLines[k + 1].trim(); k++; }
                    } else if (amountRegex.test(line) && payment.montoDetectado === "N/A") {
                        payment.montoDetectado = line.trim();
                    } else if (line.startsWith("Pagado en")) {
                        payment.pagadoEn = line.substring("Pagado en".length).trim();
                    } else if (line.startsWith("Fecha en la que se pago")) {
                        payment.originalFechaRealPago = line.substring("Fecha en la que se pago".length).replace(':', '').trim();
                         if (!payment.originalFechaRealPago && k + 1 < paymentLines.length && !["Status", "Monto:", "Pagado en"].some(key => paymentLines[k+1].startsWith(key))) {
                            payment.originalFechaRealPago = paymentLines[k+1]; k++;
                         }
                    } else if (line.startsWith("Status") && k + 1 < paymentLines.length) {
                         payment.status = paymentLines[k+1].trim(); k++;
                    }
                }
                
                if (payment.originalFechaRealPago && payment.originalFechaRealPago !== 'N/A') lastValidFechaRealPago = payment.originalFechaRealPago;
                else if (lastValidFechaRealPago) {
                    const lastDate = new Date(lastValidFechaRealPago.split(' ')[0] + 'T00:00:00Z');
                    if (!isNaN(lastDate.getTime())) {
                        lastDate.setUTCDate(lastDate.getUTCDate() + 7);
                        payment.originalFechaRealPago = lastDate.toISOString().split('T')[0];
                        lastValidFechaRealPago = payment.originalFechaRealPago;
                    }
                }

                payment.fechaRealPago = formatDateDDMMMAAAA(payment.originalFechaRealPago);
                if (payment.status.toLowerCase() === 'pagado' && payment.originalFechaRealPago !== 'N/A') {
                    const currentPaymentDate = new Date(payment.originalFechaRealPago.split(' ')[0]);
                    if (!lastPaidDate || currentPaymentDate > lastPaidDate) lastPaidDate = currentPaymentDate;
                }
                
                const today = new Date();
                const todayStr = today.toISOString().split('T')[0];
                const statusLower = payment.status.toLowerCase();

                if (statusLower === 'vencido' && !firstVencidoFound) {
                    payment.diferenciaRecalculado = calculateDateDifferenceInDays(payment.originalFechaLimitePago, todayStr);
                    payment.diferenciaOriginal = calculateDateDifferenceInDays(payment.originalFechaOriginalCalculada, todayStr);
                    firstVencidoFound = true;
                } else if (statusLower === 'por pagar' || (statusLower === 'vencido' && firstVencidoFound)) {
                    payment.diferenciaRecalculado = 'N/A';
                    payment.diferenciaOriginal = 'N/A';
                } else {
                    payment.diferenciaRecalculado = calculateDateDifferenceInDays(payment.originalFechaLimitePago, payment.originalFechaRealPago);
                    payment.diferenciaOriginal = calculateDateDifferenceInDays(payment.originalFechaOriginalCalculada, payment.originalFechaRealPago);
                }
                data.payments.push(payment);
            });
            
            const engancheData = data.payments.find(p => p.isEnganche);
            data.header.fechaCompra = engancheData ? engancheData.fechaLimitePago : (data.header.fechaCompra || 'N/A');
            data.header.fechaLiquidacionEstimada = (data.payments.length > 0) ? data.payments.at(-1).fechaLimitePago : 'N/A';
            data.header.numeroSemanasPlan = data.payments.filter(p => !p.isEnganche).length;
            data.header.ultimoPagoAplicado = lastPaidDate ? formatDateDDMMMAAAA(lastPaidDate.toISOString().split('T')[0]) : (data.header.ultimoPagoAplicado || 'N/A');

            if (data.header.fechaLiquidacionEstimada !== 'N/A') {
                const liquidacionDate = parseDateFromDDMMMAAAA(data.header.fechaLiquidacionEstimada);
                const today = new Date(); today.setUTCHours(0,0,0,0);
                if (liquidacionDate) {
                    const diffDays = Math.round((liquidacionDate - today) / (1000 * 60 * 60 * 24));
                    data.header.diferenciaLiquidacion = diffDays < 0 ? `${Math.abs(diffDays)} Dia(s) De Atraso` : `Liquida En ${diffDays} Dia(s)`;
                }
            }
            return data;
        }

        /**
         * Muestra los datos procesados en la interfaz de usuario.
         * Toma el objeto de datos y lo usa para llenar los resúmenes y tablas.
         * @param {object} dataToDisplay - El objeto con los datos procesados.
         */
        function displayData(dataToDisplay) {
            show(ui.resultsSection);
            show(ui.weeksSummary);
            show(ui.amountSummary);
            show(ui.paymentDetailsContainer);

            ui.summary.innerHTML = `
                <h2 class="font-semibold text-center mb-4 pb-2 border-b" style="border-color: var(--table-row-border);">Resumen General</h2>
                <dl class="grid grid-cols-1 md:grid-cols-3 gap-x-6 gap-y-4 text-xs sm:text-sm">
                    <div><dt>Nombre:</dt><dd>${getText(dataToDisplay.header.nombreClienteResumen)}</dd></div>
                    <div><dt>Fecha Compra:</dt><dd>${getText(dataToDisplay.header.fechaCompra)}</dd></div>
                    <div><dt>Telefono:</dt><dd>${getText(dataToDisplay.header.numeroTel)}</dd></div>
                    <div><dt>Fecha Liquidacion:</dt><dd>${getText(dataToDisplay.header.fechaLiquidacionEstimada)}</dd></div>
                    <div><dt>Folio:</dt><dd>${getText(dataToDisplay.header.folioSolicitud)}</dd></div>
                    <div><dt>Semanas:</dt><dd>${getText(dataToDisplay.header.numeroSemanasPlan)}</dd></div>
                    <div><dt>TAG:</dt><dd>${getText(dataToDisplay.header.tag)}</dd></div>
                    <div><dt>Ultimo Pago:</dt><dd>${getText(dataToDisplay.header.ultimoPagoAplicado)}</dd></div>
                    <div><dt>IMEI:</dt><dd>${getText(dataToDisplay.header.imeiResumen)}</dd></div>
                    <div class="md:col-span-1"><dt>Liquidacion:</dt><dd class="font-bold ${dataToDisplay.header.diferenciaLiquidacion?.includes('Atraso') ? 'text-diff-red' : 'text-diff-green'}">${getText(dataToDisplay.header.diferenciaLiquidacion)}</dd></div>
                </dl>`;
            
            ui.paymentTableBody.innerHTML = ''; 

            let semanasPagadas = 0, semanasVencidas = 0, semanasPorPagar = 0, pagosPuntuales = 0;
            let totalDiasAdelantoRecalculado = 0, totalDiasAtrasoRecalculado = 0;
            let totalDiasAdelantoOriginal = 0, totalDiasAtrasoOriginal = 0;
            let totalPagado = 0, totalVencido = 0, totalPorPagar = 0, totalGeneral = 0;
            const parseAmount = (amountStr) => typeof amountStr !== 'string' || amountStr === 'N/A' ? 0 : parseFloat(amountStr.replace(/[$,]/g, '')) || 0;
            const formatCurrency = (num) => '$' + num.toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 });

            if (dataToDisplay.payments && dataToDisplay.payments.length > 0) {
                dataToDisplay.payments.forEach((p, index) => {
                    const row = ui.paymentTableBody.insertRow();
                    row.className = index % 2 === 0 ? 'bg-transparent' : 'bg-gray-800/20';

                    row.insertCell().innerHTML = getText(p.etiqueta);
                    row.insertCell().textContent = getText(p.status);
                    row.insertCell().textContent = getText(p.montoDetectado);
                    const pagadoEnCell = row.insertCell();
                    pagadoEnCell.textContent = {'mic000': 'OXXO', 'mic001': 'Transferencia', 'mic002': 'SPEI'}[p.pagadoEn?.toLowerCase()] || getText(p.pagadoEn);

                    row.insertCell().textContent = getText(p.fechaRealPago);
                    row.insertCell().textContent = getText(p.fechaLimitePago);
                    row.insertCell().textContent = getText(p.fechaOriginalCalculada);

                    const diffRecalculadoCell = row.insertCell();
                    diffRecalculadoCell.className = 'font-semibold'; 
                    const diffRecalculado = p.diferenciaRecalculado;
                    if (diffRecalculado === 'N/A') diffRecalculadoCell.textContent = 'N/A';
                    else if (diffRecalculado === 0) { diffRecalculadoCell.textContent = 'Puntual'; diffRecalculadoCell.classList.add('text-diff-blue'); if (!p.isEnganche) pagosPuntuales++; }
                    else if (diffRecalculado < 0) { diffRecalculadoCell.textContent = `${Math.abs(diffRecalculado)} Dia(s) Antes`; diffRecalculadoCell.classList.add('text-diff-green'); if (!p.isEnganche) totalDiasAdelantoRecalculado += Math.abs(diffRecalculado); }
                    else { diffRecalculadoCell.textContent = `${diffRecalculado} Dia(s) Tarde`; diffRecalculadoCell.classList.add('text-diff-red'); if (!p.isEnganche) totalDiasAtrasoRecalculado += diffRecalculado; }

                    const diffOriginalCell = row.insertCell();
                    diffOriginalCell.className = 'font-semibold';
                    const diffOriginal = p.diferenciaOriginal;
                    if (diffOriginal === 'N/A') diffOriginalCell.textContent = 'N/A';
                    else if (diffOriginal === 0) { diffOriginalCell.textContent = 'En Fecha'; diffOriginalCell.classList.add('text-diff-blue'); }
                    else if (diffOriginal < 0) { diffOriginalCell.textContent = `${Math.abs(diffOriginal)} Dia(s) Antes`; diffOriginalCell.classList.add('text-diff-green'); if (!p.isEnganche) totalDiasAdelantoOriginal += Math.abs(diffOriginal); }
                    else { diffOriginalCell.textContent = `+${diffOriginal} Dia(s) Tarde`; diffOriginalCell.classList.add('text-diff-red'); if (!p.isEnganche) totalDiasAtrasoOriginal += diffOriginal; }

                    const amount = parseAmount(p.montoDetectado);
                    const status = p.status.toLowerCase();
                    if (status === 'pagado' || (p.isEnganche && status !== 'vencido' && status !== 'por pagar')) { if(!p.isEnganche) semanasPagadas++; totalPagado += amount; }
                    else if (status === 'vencido') { semanasVencidas++; totalVencido += amount; }
                    else if (status === 'por pagar') { semanasPorPagar++; totalPorPagar += amount; }
                    totalGeneral += amount;
                    Array.from(row.cells).forEach(cell => cell.classList.add('table-cell'));
                });
            }
            
            ui.amountSummary.innerHTML = `
                <h2 class="font-semibold text-center mb-3 pb-2 border-b" style="border-color: var(--table-row-border);">Resumen de Montos</h2>
                <dl class="grid grid-cols-2 gap-x-6 gap-y-4 text-xs sm:text-sm">
                    <div><dt>Total Pagado:</dt><dd class="text-diff-green font-bold">${formatCurrency(totalPagado)}</dd></div>
                    <div><dt>Total Vencido:</dt><dd class="text-diff-red font-bold">${formatCurrency(totalVencido)}</dd></div>
                    <div><dt>Total Por Pagar:</dt><dd class="text-diff-blue font-bold">${formatCurrency(totalPorPagar)}</dd></div>
                    <div><dt>Total General:</dt><dd class="font-bold">${formatCurrency(totalGeneral)}</dd></div>
                </dl>`;

            const numSemanasPlan = parseInt(dataToDisplay.header.numeroSemanasPlan) || 0;
            let porcentajeCubiertoNum = numSemanasPlan > 0 ? (semanasPagadas / numSemanasPlan) * 100 : 0;
            const porcentajeCubiertoStr = porcentajeCubiertoNum > 0 ? porcentajeCubiertoNum.toFixed(1) + "%" : "0.0%";
            let progressBarColor = porcentajeCubiertoNum > 75 ? '#ADFF2F' : (porcentajeCubiertoNum > 45 ? '#6B8E23' : 'var(--success-bg)');

            const balanceRecalculado = totalDiasAdelantoRecalculado - totalDiasAtrasoRecalculado;
            const balanceOriginal = totalDiasAdelantoOriginal - totalDiasAtrasoOriginal;
            let atrasoHtml = '';
            if (semanasVencidas > 0 && dataToDisplay.payments) {
                const firstOverdue = dataToDisplay.payments.find(p => p.status.toLowerCase() === 'vencido');
                if (firstOverdue) {
                    const weekNumber = firstOverdue.etiqueta.match(/\d+/)?.[0] || '';
                    if(weekNumber){
                         atrasoHtml = `<div class="mt-3 text-center text-xs sm:text-sm text-diff-red font-semibold">Atraso desde la semana: ${weekNumber}</div>`;
                        const limitDateStr = firstOverdue.originalFechaLimitePago;
                        if (limitDateStr && limitDateStr !== 'N/A') {
                            const today = new Date(); today.setUTCHours(0, 0, 0, 0);
                            const limitDate = new Date(limitDateStr + 'T00:00:00Z');
                            if (!isNaN(limitDate.getTime())) {
                                const diffDays = Math.ceil((today.getTime() - limitDate.getTime()) / (1000 * 60 * 60 * 24));
                                if (diffDays > 0) atrasoHtml += `<div class="mt-1 text-center text-xs sm:text-sm text-diff-red font-semibold">(${diffDays} dia(s) de atraso)</div>`;
                            }
                        }
                    }
                }
            }

            dataToDisplay.summaryDetails = { porcentajeCubiertoStr, balanceRecalculado, balanceOriginal };
            dataToDisplay.header.balance = balanceRecalculado;

            ui.weeksSummary.innerHTML = `
                <h2 class="font-semibold text-center mb-3 pb-2 border-b" style="border-color: var(--table-row-border);">Semanas y Puntualidad</h2>
                <div class="grid grid-cols-4 gap-4 text-center text-xs sm:text-sm">
                    <div><dt class="opacity-70">Pagadas:</dt><dd class="text-diff-green font-bold text-lg">${semanasPagadas}</dd></div>
                    <div><dt class="opacity-70">Vencidas:</dt><dd class="text-diff-red font-bold text-lg">${semanasVencidas}</dd></div>
                    <div><dt class="opacity-70">Por Pagar:</dt><dd class="text-diff-blue font-bold text-lg">${semanasPorPagar}</dd></div>
                    <div><dt class="opacity-70">Puntuales:</dt><dd class="text-diff-blue font-bold text-lg">${pagosPuntuales}</dd></div>
                </div>
                <div class="mt-4 grid grid-cols-1 md:grid-cols-2 gap-x-6 gap-y-4 border-t border-b py-3" style="border-color: var(--table-row-border);">
                    <div>
                        <h3 class="font-semibold text-center text-sm mb-2 opacity-80">Balance vs. Fecha Recalculada</h3>
                        <div class="grid grid-cols-3 gap-2 text-center text-xs">
                            <div><dt class="opacity-70">Adelanto:</dt><dd class="text-diff-green font-bold text-lg">${totalDiasAdelantoRecalculado}</dd></div>
                            <div><dt class="opacity-70">Atraso:</dt><dd class="text-diff-red font-bold text-lg">${totalDiasAtrasoRecalculado}</dd></div>
                            <div><dt class="opacity-70">Balance:</dt><dd class="font-semibold text-lg ${balanceRecalculado > 0 ? 'text-diff-green' : balanceRecalculado < 0 ? 'text-diff-red' : 'text-diff-blue'}">${balanceRecalculado > 0 ? `+${balanceRecalculado}` : balanceRecalculado} d</dd></div>
                        </div>
                    </div>
                    <div>
                        <h3 class="font-semibold text-center text-sm mb-2 opacity-80">Balance vs. Fecha Original</h3>
                         <div class="grid grid-cols-3 gap-2 text-center text-xs">
                            <div><dt class="opacity-70">Adelanto:</dt><dd class="text-diff-green font-bold text-lg">${totalDiasAdelantoOriginal}</dd></div>
                            <div><dt class="opacity-70">Atraso:</dt><dd class="text-diff-red font-bold text-lg">${totalDiasAtrasoOriginal}</dd></div>
                            <div><dt class="opacity-70">Balance:</dt><dd class="font-semibold text-lg ${balanceOriginal > 0 ? 'text-diff-green' : balanceOriginal < 0 ? 'text-diff-red' : 'text-diff-blue'}">${balanceOriginal > 0 ? `+${balanceOriginal}` : balanceOriginal} d</dd></div>
                        </div>
                    </div>
                </div>
                <div class="mt-4">
                    <div class="flex justify-between mb-1 text-sm"><span class="font-medium opacity-70">Progreso Cubierto</span><span class="font-semibold">${porcentajeCubiertoStr}</span></div>
                    <div class="w-full rounded-full h-2.5" style="background-color: var(--table-row-border);"><div class="h-2.5 rounded-full" style="width: ${porcentajeCubiertoNum}%; background-color: ${progressBarColor};"></div></div>
                    ${atrasoHtml}
                </div>`;

            ui.rebuyCandidateSection.classList.toggle('hidden', porcentajeCubiertoNum < 75);
            ui.referCandidateSection.classList.toggle('hidden', porcentajeCubiertoNum < 45);
            
            ui.errorMessage.classList.toggle('hidden', !dataToDisplay.errors?.length);
            if(dataToDisplay.errors?.length) ui.errorMessage.innerHTML = '<strong>Errores:</strong><ul>' + dataToDisplay.errors.map(e => `<li>${e}</li>`).join('') + '</ul>';
        }

        /**
         * Renderiza la tabla de historial de análisis guardados.
         */
        function renderHistoryTable() {
            ui.historyTableBody.innerHTML = '';
            if (savedSummaries.length > 0) show(ui.historySection);
            else hide(ui.historySection);
            
            savedSummaries.forEach((summary, index) => {
                const row = ui.historyTableBody.insertRow();
                row.insertCell().textContent = getText(summary.header.tag);
                row.insertCell().textContent = getText(summary.header.nombreClienteResumen);
                const balanceCell = row.insertCell();
                const balance = summary.header.balance || 0;
                balanceCell.textContent = `${balance >= 0 ? '+' : ''}${balance} días`;
                balanceCell.className = `font-semibold ${balance >= 0 ? 'text-diff-green' : 'text-diff-red'}`;
                row.insertCell().innerHTML = `<button class="btn btn-small" onclick="window.showHistoryDetailsActivos(${index})"><i class="far fa-eye mr-1"></i>Ver</button>`;
                Array.from(row.cells).forEach(cell => cell.classList.add('table-cell'));
            });
            ui.viewHistorySummaryBtn.classList.toggle('hidden', savedSummaries.length === 0);
        }
        
        /**
         * Función principal que se ejecuta al hacer clic en "Analizar Datos".
         * Orquesta la lectura de los inputs, el procesamiento y la visualización de resultados.
         */
        scope.processAllData = function() {
            try {
                hide(ui.errorMessage);
                const structuredText = ui.structuredDataInput.value;
                const rawText = ui.rawData.value;

                let headerData = {};
                if (structuredText.trim()) {
                    const lines = structuredText.trim().split('\n');
                    if (lines.length > 0) {
                        const headers = (lines.length === 1) ? ["FechaFolio", "Folio Solicitud", "TAG", "Inversion", "Marca", "Modelo", "IMEI", "Cliente", "Numero", "Version INEQ"] : lines[0].split(/\t/).map(h => h.trim());
                        const values = (lines.length === 1) ? lines[0].split(/\t/) : lines[1].split(/\t/).map(v => v.trim());
                        const structuredDataMap = {};
                        headers.forEach((header, index) => {
                            structuredDataMap[header.toLowerCase().replace(/\s+/g, '').replace(/[áéíóú]/g, m => ({'á':'a','é':'e','í':'i','ó':'o','ú':'u'})[m]||m)] = values[index]||null;
                        });
                        headerData.tag = structuredDataMap['tag'] || structuredDataMap['tagproteccionderobo'];
                        headerData.nombreClienteResumen = structuredDataMap['cliente'] || structuredDataMap['nombredelcliente'];
                        headerData.imeiResumen = structuredDataMap['imei'];
                        headerData.folioSolicitud = structuredDataMap['foliosolicitud'] || structuredDataMap['solicitud'];
                        headerData.numeroTel = structuredDataMap['numero'] || structuredDataMap['numero'];
                        headerData.fechaCompra = structuredDataMap['fechafolio'] ? formatDateDDMMMAAAA(structuredDataMap['fechafolio'].split(' ')[0]) : null;
                        headerData.inversion = structuredDataMap['inversion'];
                        headerData.versionIneq = structuredDataMap['versionineq'];
                        headerData.marcaModelo = `${getText(structuredDataMap['marca'])} ${getText(structuredDataMap['modelo'])}`.trim();
                    }
                }
                
                currentProcessedData = rawText.trim() || structuredText.trim() ? parseCalendarData(rawText, headerData) : { header: headerData, payments: [], errors: [] };
                displayData(currentProcessedData);
                show(ui.saveAndClearBtn);
                show(ui.captureButton);
            } catch (error) {
                console.error("Error procesando datos de activos:", error);
                ui.errorMessage.textContent = 'Ocurrió un error al procesar los datos. Revise la consola para más detalles.';
                show(ui.errorMessage);
            }
        }
        
        /**
         * Guarda el análisis actual en el historial y limpia los campos de entrada y los resultados.
         */
        scope.saveAndClear = function() {
            if (currentProcessedData) {
                savedSummaries.push(JSON.parse(JSON.stringify(currentProcessedData)));
                renderHistoryTable();
            }
            ui.rawData.value = '';
            ui.structuredDataInput.value = '';
            hide(ui.resultsSection);
            hide(ui.errorMessage);
            hide(ui.amountSummary);
            currentProcessedData = null;
            hide(ui.saveAndClearBtn);
            hide(ui.captureButton);
        }
        
        /**
         * Genera una captura de pantalla del área de resultados y la descarga como un archivo PNG.
         */
        scope.generateCapture = function() {
            const captureTarget = ui.captureTarget;
            if (ui.resultsSection.classList.contains('hidden')) {
                ui.errorMessage.textContent = 'Primero procese los datos para generar una captura.';
                show(ui.errorMessage);
                return;
            }
            const data = currentProcessedData.header;
            const fileName = `${data.tag || 'SIN_TAG'}-${(data.nombreClienteResumen || 'SIN_NOMBRE').replace(/\s+/g, '_')}.png`;

            html2canvas(captureTarget, {
                allowTaint: true,
                useCORS: true,
                scale: 3,
                backgroundColor: 'rgba(0,0,0,0)'
            }).then(canvas => {
                const link = document.createElement('a');
                link.download = fileName;
                link.href = canvas.toDataURL('image/png');
                link.click();
            }).catch(err => {
                 console.error("Error al generar la captura: ", err);
                 ui.errorMessage.innerHTML = 'Error al generar la captura.';
                 show(ui.errorMessage);
            });
        }

        /**
         * Abre un modal que muestra una tabla resumen con todos los análisis guardados en el historial.
         */
        scope.openHistorySummary = function() {
            ui.historySummaryTableBody.innerHTML = '';
            savedSummaries.forEach(summary => {
                const row = ui.historySummaryTableBody.insertRow();
                const h = summary.header; const d = summary.summaryDetails || {};
                row.insertCell().textContent = getText(h.tag); row.insertCell().textContent = getText(h.nombreClienteResumen);
                row.insertCell().textContent = getText(h.folioSolicitud); row.insertCell().textContent = getText(h.fechaCompra);
                row.insertCell().textContent = getText(h.fechaLiquidacionEstimada); row.insertCell().textContent = getText(h.inversion);
                row.insertCell().textContent = getText(h.versionIneq); row.insertCell().textContent = getText(h.marcaModelo);
                row.insertCell().textContent = getText(d.porcentajeCubiertoStr);
                const balanceRecalculadoCell = row.insertCell(); balanceRecalculadoCell.className = `font-semibold ${d.balanceRecalculado > 0 ? 'text-diff-green' : d.balanceRecalculado < 0 ? 'text-diff-red' : 'text-diff-blue'}`; balanceRecalculadoCell.textContent = d.balanceRecalculado > 0 ? `+${d.balanceRecalculado} Dias` : d.balanceRecalculado < 0 ? `${d.balanceRecalculado} Dias` : '0 d';
                const balanceOriginalCell = row.insertCell(); balanceOriginalCell.className = `font-semibold ${d.balanceOriginal > 0 ? 'text-diff-green' : d.balanceOriginal < 0 ? 'text-diff-red' : 'text-diff-blue'}`; balanceOriginalCell.textContent = d.balanceOriginal > 0 ? `+${d.balanceOriginal} Dias` : d.balanceOriginal < 0 ? `${d.balanceOriginal} Dias` : '0 d';
                Array.from(row.cells).forEach(cell => cell.classList.add('table-cell', 'text-center'));
            });
            ui.historySummaryModal.style.display = 'flex';
        }

        /**
         * Cierra el modal del resumen del historial.
         */
        scope.closeHistorySummary = function() { if(ui.historySummaryModal) ui.historySummaryModal.style.display = 'none'; }
        
        /**
         * Descarga el resumen del historial como un archivo CSV.
         */
        scope.downloadHistorySummary = function() {
            if (savedSummaries.length === 0) return;
            const headers = ["TAG", "Nombre", "FOLIO", "Fecha Compra", "Fecha Liquidacion", "Inversion", "Version INEQ", "Marca y modelo", "Progreso Cubierto", "Balance vs. Fecha Limite", "Balance vs. Fecha Original"];
            let csvContent = headers.join(",") + "\r\n";
            savedSummaries.forEach(summary => {
                const h = summary.header; const d = summary.summaryDetails || {};
                const row = [getText(h.tag), getText(h.nombreClienteResumen), getText(h.folioSolicitud), getText(h.fechaCompra), getText(h.fechaLiquidacionEstimada), getText(h.inversion), getText(h.versionIneq), getText(h.marcaModelo), getText(d.porcentajeCubiertoStr), getText(d.balanceRecalculado), getText(d.balanceOriginal)];
                csvContent += row.map(val => `"${String(val).replace(/"/g, '""')}"`).join(",") + "\r\n";
            });
            const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement("a");
            link.setAttribute("href", URL.createObjectURL(blob));
            link.setAttribute("download", "resumen_historial_activos.csv");
            link.style.visibility = 'hidden';
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }

        /**
         * Muestra los detalles de un análisis específico del historial.
         * @param {number} index - El índice del análisis en el array `savedSummaries`.
         */
        scope.showHistoryDetails = (index) => {
            if (savedSummaries[index]) {
                currentProcessedData = savedSummaries[index];
                displayData(currentProcessedData);
                show(ui.saveAndClearBtn);
                show(ui.captureButton);
            }
        };

        if(ui.captureButton) {
            ui.captureButton.addEventListener('click', scope.generateCapture);
        }

    })();

    // --- SCRIPT PARA LA PESTAÑA DE CALENDARIO DE LIQUIDADOS ---
    ;(() => {
        const scope = {};
        document.getElementById('liquidados')._scriptScope = scope;

        let currentProcessedData = null;
        let historyLog = [];

        const ui = {
            processButton: document.getElementById('processButtonLiquidados'),
            archiveButton: document.getElementById('archiveButtonLiquidados'),
            captureButton: document.getElementById('captureButtonLiquidados'),
            clientDataElement: document.getElementById('clientDataLiquidados'),
            inputTextElement: document.getElementById('inputTextLiquidados'),
            resultsContent: document.getElementById('resultsContentLiquidados'),
            summarySection: document.getElementById('summarySectionLiquidados'),
            errorMessageElement: document.getElementById('errorMessageLiquidados'),
            tableBody: document.getElementById('paymentTableBodyLiquidados'),
            historySection: document.getElementById('historySectionLiquidados'),
            historyTableBody: document.getElementById('historyTableBodyLiquidados'),
            viewHistoryBtn: document.getElementById('viewHistoryBtnLiquidados'),
            historyModal: document.getElementById('historyModalLiquidados'),
            closeHistoryModal: document.getElementById('closeHistoryModalLiquidados'),
            historySummaryTableBody: document.getElementById('historySummaryTableBodyLiquidados'),
            resultsTableContainer: document.getElementById('resultsTableContainerLiquidados'),
            captureTarget: document.getElementById('captureTargetLiquidados'),
        };

        /**
         * Formatea un número a una cadena de texto de moneda (ej. $1,234.56).
         * @param {number} number - El número a formatear.
         * @returns {string} La cadena de texto formateada.
         */
        function formatCurrency(number) {
            return !isNaN(number) && number !== null ? `$${number.toFixed(2).replace(/\B(?=(\d{3})+(?!\d))/g, ',')}` : "---";
        }

        /**
         * Actualiza la sección de resumen en la interfaz de usuario con los datos proporcionados.
         * @param {object} data - Los datos del resumen de la cuenta liquidada.
         */
        function updateSummaryUI(data) {
            const { tag, folio, nombreCliente, imei, fechaCompra, fechaLiquidacion, semanasPlan, ultimoPago, diferenciaLiquidacion, dat, advertenciaBeneficios, montoEnganche, montoSemanas, montoTotal } = data;
            ui.summarySection.innerHTML = `
                <h2 class="font-semibold text-center mb-4 pb-2 border-b" style="border-color: var(--table-row-border);">Resumen General</h2>
                <dl class="grid grid-cols-1 md:grid-cols-3 gap-x-6 gap-y-4 text-xs sm:text-sm">
                    <div><dt>TAG:</dt><dd>${getText(tag)}</dd></div>
                    <div><dt>Folio:</dt><dd>${getText(folio)}</dd></div>
                    <div><dt>Nombre:</dt><dd>${getText(nombreCliente)}</dd></div>
                    <div><dt>IMEI:</dt><dd>${getText(imei)}</dd></div>
                    <div class="${dat ? '' : 'hidden'}"><dt>DAT:</dt><dd class="font-bold text-diff-red">${getText(dat)}</dd></div>
                    <div><dt>Fecha de Compra:</dt><dd>${getText(fechaCompra)}</dd></div>
                    <div><dt>Fecha de Liquidacion:</dt><dd>${getText(fechaLiquidacion)}</dd></div>
                    <div><dt>Semanas (Plan):</dt><dd>${getText(semanasPlan)}</dd></div>
                    <div><dt>Ultimo Pago Aplicado:</dt><dd>${getText(ultimoPago)}</dd></div>
                    <div class="${diferenciaLiquidacion ? '' : 'hidden'}"><dt>Diferencia Liquidacion:</dt><dd class="font-bold ${diferenciaLiquidacion?.includes('atraso') ? 'text-diff-red' : 'text-diff-green'}">${getText(diferenciaLiquidacion)}</dd></div>
                </dl>
                <dl class="grid grid-cols-1 md:grid-cols-3 gap-x-6 gap-y-4 text-xs sm:text-sm mt-4 border-t pt-4" style="border-color: var(--table-row-border);">
                    <div><dt>Monto Enganche:</dt><dd class="text-diff-green">${getText(montoEnganche)}</dd></div>
                    <div><dt>Monto Semanas Pagadas:</dt><dd class="text-diff-green">${getText(montoSemanas)}</dd></div>
                    <div class="md:col-span-3"><dt>Monto Pagado Total:</dt><dd class="text-diff-green font-bold text-lg">${getText(montoTotal)}</dd></div>
                </dl>
                ${advertenciaBeneficios ? `<p class="mt-4 text-xs font-medium text-diff-red p-3 rounded-lg" style="background-color: var(--danger-bg);">${advertenciaBeneficios}</p>` : ''}
            `;
        }
        
        /**
         * Renderiza la tabla de detalles de pago.
         * @param {Array<object>} payments - Un array de objetos, cada uno representando un pago.
         */
        function renderPaymentTable(payments) {
            ui.tableBody.innerHTML = '';
            if (!payments || payments.length === 0) {
                 ui.tableBody.innerHTML = `<tr><td colspan="6" class="table-cell text-center opacity-70">No hay detalles de pago para mostrar.</td></tr>`;
                 return;
            };
            payments.forEach((record, index) => {
                const dueDate = new Date(record.dueDateString + "T00:00:00Z");
                const paymentDate = new Date(record.paymentDateTimeString.split(' ')[0] + "T00:00:00Z");
                const dayDifference = Math.round((paymentDate - dueDate) / 864e5);
                let dayDifferenceText, dayDiffClass;
                if (dayDifference > 0) { dayDifferenceText = `${dayDifference} tarde`; dayDiffClass = 'text-diff-red';}
                else if (dayDifference < 0) { dayDifferenceText = `${Math.abs(dayDifference)} antes`; dayDiffClass = 'text-diff-green'; }
                else { dayDifferenceText = `A tiempo`; dayDiffClass = 'text-diff-blue';}
                
                const newRow = ui.tableBody.insertRow();
                 newRow.className = index % 2 === 0 ? 'bg-transparent' : 'bg-gray-800/20';

                newRow.innerHTML = `<td class="table-cell">${getText(record.label)}</td><td class="table-cell">${formatDateDDMMMAAAA(record.dueDateString)}</td><td class="table-cell">${getText(record.amountDisplay)}</td><td class="table-cell">${getText(record.status)}</td><td class="table-cell">${formatDateDDMMMAAAA(record.paymentDateTimeString)}</td><td class="table-cell font-semibold ${dayDiffClass}">${dayDifferenceText}</td>`;
            });
        }

        /**
         * Resetea la interfaz de usuario a su estado inicial, opcionalmente limpiando los campos de entrada.
         * @param {boolean} [clearInputs=false] - Si es true, limpia los textareas.
         */
        function resetUI(clearInputs = false) {
            hide(ui.resultsContent);
            hide(ui.errorMessageElement);
            hide(ui.archiveButton);
            hide(ui.captureButton);
            currentProcessedData = null;
            if(clearInputs) {
                ui.clientDataElement.value = '';
                ui.inputTextElement.value = '';
            }
        }

        /**
         * Renderiza la tabla del historial de cuentas liquidadas.
         */
        function renderHistoryTable() {
            ui.historyTableBody.innerHTML = '';
            if(historyLog.length === 0) { hide(ui.historySection); return; }
            show(ui.historySection);
            
            historyLog.forEach((log, index) => {
                const diffText = log.summary.diferenciaLiquidacion || '0';
                const diffMatch = diffText.match(/-?\d+/); const diffDays = diffMatch ? parseInt(diffMatch[0], 10) : 0;
                let balanceText, balanceClass;
                if (diffText.includes('antes')) { balanceText = `+${Math.abs(diffDays)} dias`; balanceClass = 'text-diff-green'; }
                else if (diffText.includes('atraso')) { balanceText = `-${diffDays} dias`; balanceClass = 'text-diff-red'; }
                else { balanceText = '+0 dias'; balanceClass = 'text-diff-blue'; }
                const row = ui.historyTableBody.insertRow();
                row.innerHTML = `<td class="table-cell">${getText(log.summary.tag, 'N/A')}</td><td class="table-cell">${getText(log.summary.nombreCliente, 'N/A')}</td><td class="table-cell ${balanceClass} font-semibold">${balanceText}</td><td class="table-cell"><button class="btn btn-small view-history-btn-liquidados" data-index="${index}"><i class="fas fa-eye mr-1"></i>Ver</button></td>`;
            });
             ui.viewHistoryBtn.classList.toggle('hidden', historyLog.length === 0);
        }
        
        if (ui.historyTableBody) {
            ui.historyTableBody.addEventListener('click', (event) => {
                const button = event.target.closest('.view-history-btn-liquidados');
                if (button) {
                    const index = button.dataset.index;
                    viewLogEntry(index);
                }
            });
        }

        /**
         * Abre el modal que muestra el resumen completo del historial de liquidados.
         */
        function openHistoryModal() {
            if (!ui.historySummaryTableBody) return;
            ui.historySummaryTableBody.innerHTML = '';
            historyLog.forEach(log => {
                const s = log.summary;
                const row = ui.historySummaryTableBody.insertRow();
                row.innerHTML = `<td class="table-cell">${getText(s.tag)}</td><td class="table-cell">${getText(s.nombreCliente)}</td><td class="table-cell">${getText(s.folio)}</td><td class="table-cell">${getText(s.imei)}</td><td class="table-cell">${getText(s.fechaCompra)}</td><td class="table-cell">${getText(s.fechaLiquidacion)}</td><td class="table-cell">${getText(s.semanasPlan)}</td><td class="table-cell">${getText(s.ultimoPago)}</td><td class="table-cell">${getText(s.diferenciaLiquidacion)}</td>`;
            });
            show(ui.historyModal);
        }

        /**
         * Guarda el análisis actual en el historial y limpia la interfaz.
         */
        function archiveAndClear() {
            if (currentProcessedData) { historyLog.push(currentProcessedData); renderHistoryTable(); }
            resetUI(true);
        }

        /**
         * Muestra los detalles de una entrada específica del historial.
         * @param {number} index - El índice de la entrada en `historyLog`.
         */
        function viewLogEntry(index) {
            const logEntry = historyLog[index];
            if (logEntry) {
                currentProcessedData = logEntry;
                updateSummaryUI(logEntry.summary);
                renderPaymentTable(logEntry.payments);
                show(ui.resultsContent);
                show(ui.archiveButton);
                show(ui.captureButton);
            }
        }
        
        /**
         * Genera una captura de pantalla del área de resultados y la descarga como PNG.
         */
        scope.generateCapture = function() {
             const captureTarget = ui.captureTarget;
            if (ui.resultsContent.classList.contains('hidden')) {
                ui.errorMessageElement.textContent = 'Primero procese los datos para generar una captura.';
                show(ui.errorMessageElement);
                return;
            }
            const data = currentProcessedData.summary;
            const fileName = `${data.tag || 'SIN_TAG'}-${(data.nombreCliente || 'SIN_NOMBRE').replace(/\s+/g, '_')}.png`;

            html2canvas(captureTarget, {
                allowTaint: true,
                useCORS: true,
                scale: 3,
                backgroundColor: 'rgba(0,0,0,0)'
            }).then(canvas => {
                const link = document.createElement('a');
                link.download = fileName;
                link.href = canvas.toDataURL('image/png');
                link.click();
            }).catch(err => {
                 console.error("Error al generar la captura: ", err);
                 ui.errorMessageElement.innerHTML = 'Error al generar la captura.';
                 show(ui.errorMessageElement);
            });
        }

        /**
         * Procesa el estado de cuenta de un cliente liquidado.
         */
        function processStatement() {
            resetUI();
            const clientDataText = ui.clientDataElement.value.trim();
            const inputText = ui.inputTextElement.value.trim();
            
            let summary = {};
            let payments = [];
            
            try {
                if (clientDataText) {
                    summary.folio = clientDataText.match(/^\d{10,20}\b/)?.[0] || null;
                    summary.imei = clientDataText.match(/\b(\d{15})\b/)?.[1] || null;
                    summary.semanasPlan = clientDataText.match(/\b(\d{1,2})S\b/)?.[0] ? `${parseInt(clientDataText.match(/\b(\d{1,2})S\b/)[1], 10)} semanas` : null;
                    summary.nombreCliente = clientDataText.match(/\b\d{10,20}\b\s+(.+?)\s+\b[A-Z]{4}\d{6}/)?.[1].trim() || null;
                    const datMatch = clientDataText.match(/(\d+)\s*DIAS\s*DE\s*ATRASO/i);
                    if (datMatch) summary.dat = `${datMatch[1]} dia(s)`;
                }
                if (inputText) {
                    summary.tag = inputText.match(/Estado de cuenta TAG:\s*([A-Z0-9]+)/i)?.[1] || null;
                    const paymentRecordRegex = /Fecha Pago:\s*(\d{4}-\d{2}-\d{2})\s*Monto:\s*\$?([\d,]+\.?\d*)\s*Status\s*Pagado el dia\s*(\d{4}-\d{2}-\d{2}\s\d{2}:\d{2}:\d{2})/gis;
                    let match; let totalAmountPaid = 0, downPaymentAmount = 0, totalWeeklyPayments = 0;
                    while ((match = paymentRecordRegex.exec(inputText)) !== null) {
                        const amountValue = parseFloat(match[2].trim().replace(/,/g, ''));
                        if (!isNaN(amountValue)) {
                            totalAmountPaid += amountValue;
                            if (payments.length === 0) downPaymentAmount = amountValue; else totalWeeklyPayments += amountValue;
                        }
                        payments.push({ dueDateString: match[1], amountDisplay: formatCurrency(amountValue), paymentDateTimeString: match[3], status: "Pagado", label: (payments.length === 0) ? "Enganche" : `Semana ${String(payments.length).padStart(2, '0')}` });
                    }
                    summary.montoEnganche = formatCurrency(downPaymentAmount);
                    summary.montoSemanas = formatCurrency(totalWeeklyPayments);
                    summary.montoTotal = formatCurrency(totalAmountPaid);
                    if (payments.length > 0) {
                        summary.fechaCompra = formatDateDDMMMAAAA(payments[0].paymentDateTimeString);
                        summary.ultimoPago = formatDateDDMMMAAAA(payments.at(-1).paymentDateTimeString);
                    }
                }
                const weeksCountNum = summary.semanasPlan ? parseInt(summary.semanasPlan) : 0;
                if (summary.fechaCompra && weeksCountNum > 0 && payments.length > 0) {
                    const purchaseDate = new Date(payments[0].paymentDateTimeString.split(' ')[0] + "T00:00:00Z");
                    const settlementDate = new Date(purchaseDate); settlementDate.setUTCDate(purchaseDate.getUTCDate() + (weeksCountNum * 7));
                    summary.fechaLiquidacion = formatDateDDMMMAAAA(settlementDate.toISOString().split('T')[0]);
                    const lastPaymentDate = new Date(payments.at(-1).paymentDateTimeString.split(' ')[0] + "T00:00:00Z");
                    const dayDiff = Math.round((lastPaymentDate - settlementDate) / 864e5);
                    if (dayDiff > 0) {
                       summary.diferenciaLiquidacion = `${dayDiff} dia(s) de atraso`;
                       if (!summary.dat) summary.dat = `${dayDiff} dia(s)`;
                       if (dayDiff > 140) summary.advertenciaBeneficios = "Cliente pierde sus beneficios de Referidos y Recompra.";
                    } else {
                       if (!summary.dat) summary.dat = '0 dia(s)';
                       summary.diferenciaLiquidacion = dayDiff < 0 ? `Liquidado ${Math.abs(dayDiff)} dia(s) antes` : "Liquidado a tiempo";
                    }
                }
                currentProcessedData = { summary, payments };
                show(ui.resultsContent);
                updateSummaryUI(summary);
                renderPaymentTable(payments);
                show(ui.archiveButton);
                show(ui.captureButton);

            } catch (error) {
                console.error("Error procesando:", error);
                ui.errorMessageElement.textContent = 'Ocurrio un error. Revisa la consola.';
                show(ui.errorMessageElement);
            }
        }
        
        /**
         * Descarga el historial de cuentas liquidadas como un archivo CSV.
         */
        window.downloadHistorySummaryLiquidados = function() {
            if (historyLog.length === 0) return;
            const headers = ["TAG", "Nombre", "Folio", "IMEI", "F. Compra", "F. Liquid.", "Semanas", "Ult. Pago", "Dif. Liquid."];
            let csvContent = headers.join(",") + "\r\n";
            historyLog.forEach(log => {
                const s = log.summary;
                const row = [
                    getText(s.tag), getText(s.nombreCliente), getText(s.folio), getText(s.imei),
                    getText(s.fechaCompra), getText(s.fechaLiquidacion), getText(s.semanasPlan),
                    getText(s.ultimoPago), getText(s.diferenciaLiquidacion)
                ];
                csvContent += row.map(val => `"${String(val).replace(/"/g, '""')}"`).join(",") + "\r\n";
            });
            const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement("a");
            const url = URL.createObjectURL(blob);
            link.setAttribute("href", url);
            link.setAttribute("download", "resumen_historial_liquidados.csv");
            link.style.visibility = 'hidden';
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }
        
        if(ui.processButton) ui.processButton.addEventListener('click', processStatement);
        if(ui.archiveButton) ui.archiveButton.addEventListener('click', archiveAndClear);
        if(ui.captureButton) {
            ui.captureButton.addEventListener('click', scope.generateCapture);
        }
        if(ui.viewHistoryBtn) ui.viewHistoryBtn.addEventListener('click', openHistoryModal);
        if(ui.closeHistoryModal) ui.closeHistoryModal.addEventListener('click', () => hide(ui.historyModal));
        if(ui.historyModal) ui.historyModal.addEventListener('click', (e) => { if (e.target === ui.historyModal) hide(ui.historyModal); });
        resetUI();
    })();

    // --- SCRIPT PARA LA PESTAÑA DE SEPARADOR DE CARTERAS ---
    ;(() => {
        const excelFileInput = document.getElementById('excelFileInputSeparador');
        const processButton = document.getElementById('processButtonSeparador');
        const messageElem = document.getElementById('messageSeparador');
        const fileLabel = document.getElementById('excelFileSeparadorLabel');
        const summaryContainer = document.getElementById('summaryContainerSeparador');
        const summaryTableBody = document.getElementById('summaryTableBodySeparador');

        if(excelFileInput) {
            excelFileInput.addEventListener('change', () => {
                if (excelFileInput.files.length > 0) {
                    fileLabel.textContent = excelFileInput.files[0].name;
                    processButton.disabled = false;
                    messageElem.textContent = 'Archivo listo para procesar.';
                    messageElem.className = 'mt-6 font-semibold p-3 rounded-lg';
                    messageElem.style.backgroundColor = 'var(--info-bg)';
                    hide(summaryContainer);
                } else {
                    fileLabel.textContent = 'Seleccionar Archivo .xlsx';
                    processButton.disabled = true;
                    messageElem.textContent = '';
                }
            });
        }

        if(processButton) {
            processButton.addEventListener('click', async () => {
                messageElem.textContent = '';
                processButton.disabled = true;
                processButton.querySelector('span').textContent = 'Procesando...';
                
                const file = excelFileInput.files[0];
                if (!file) {
                    messageElem.textContent = 'Por favor, selecciona un archivo XLSX.';
                    messageElem.style.backgroundColor = 'var(--danger-bg)';
                    processButton.disabled = false;
                    processButton.querySelector('span').textContent = 'Procesar y Descargar ZIP';
                    return;
                }
                
                messageElem.textContent = 'Procesando el archivo...';
                messageElem.style.backgroundColor = 'var(--info-bg)';

                try {
                    const data = await file.arrayBuffer();
                    const workbook = XLSX.read(data, { type: 'array' });
                    const worksheet = workbook.Sheets[workbook.SheetNames[0]];
                    const rawData = XLSX.utils.sheet_to_json(worksheet, { header: 1 });

                    if (rawData.length < 2) throw new Error('El archivo Excel esta vacio o no tiene datos.');

                    const headers = rawData[0];
                    const rows = rawData.slice(1);
                    const normalizedHeaders = headers.map(h => typeof h === 'string' ? h.trim().toLowerCase() : h);
                    const datsColumnIndex = normalizedHeaders.findIndex(header => header === "dat's");

                    if (datsColumnIndex === -1) throw new Error('No se encontro la columna "Dat\'s".');
                    
                    const groupedData = new Map();
                    for (const row of rows) {
                        if (row.length > datsColumnIndex) {
                            let datsValue = (row[datsColumnIndex] === null || row[datsColumnIndex] === undefined) ? 'SinDats' : String(row[datsColumnIndex]).trim();
                            if (!groupedData.has(datsValue)) groupedData.set(datsValue, []);
                            groupedData.get(datsValue).push(row);
                        }
                    }
                    
                    summaryTableBody.innerHTML = '';
                    let totalAccounts = 0;
                    for (const [datsKey, groupRows] of groupedData.entries()) {
                        const row = summaryTableBody.insertRow();
                        row.insertCell().textContent = datsKey;
                        row.insertCell().textContent = groupRows.length;
                        totalAccounts += groupRows.length;
                        row.cells[0].className = 'table-cell text-left';
                        row.cells[1].className = 'table-cell text-right font-semibold';
                    }
                    const totalRow = summaryTableBody.insertRow();
                    totalRow.className = "font-bold";
                    totalRow.insertCell().textContent = "Total";
                    totalRow.insertCell().textContent = totalAccounts;
                    totalRow.cells[0].className = 'table-cell text-left';
                    totalRow.cells[1].className = 'table-cell text-right';
                    show(summaryContainer);

                    const zip = new JSZip();
                    for (const [datsKey, groupRows] of groupedData.entries()) {
                        const dataToXlsx = [headers, ...groupRows];
                        const newWorkbook = XLSX.utils.book_new();
                        const newWorksheet = XLSX.utils.aoa_to_sheet(dataToXlsx);
                        XLSX.utils.book_append_sheet(newWorkbook, newWorksheet, "Datos");
                        const xlsxOutput = XLSX.write(newWorkbook, { bookType: 'xlsx', type: 'array' });
                        const sanitizedDatsKey = datsKey.replace(/[\\/:*?"<>|']/g, '').replace(/\s+/g, '_');
                        zip.file(`${sanitizedDatsKey}.xlsx`, new Blob([xlsxOutput], { type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet' }));
                    }
                    
                    messageElem.textContent = 'Generando archivo ZIP...';
                    const zipBlob = await zip.generateAsync({ type: "blob" });
                    saveAs(zipBlob, "datos_agrupados.zip");
                    messageElem.textContent = '¡Procesamiento completado y ZIP descargado!';
                    messageElem.style.backgroundColor = 'var(--success-bg)';

                } catch (error) {
                    console.error('Error al procesar el archivo:', error);
                    messageElem.textContent = `Error: ${error.message}.`;
                    messageElem.style.backgroundColor = 'var(--danger-bg)';
                    hide(summaryContainer);
                } finally {
                    processButton.disabled = false;
                    processButton.querySelector('span').textContent = 'Procesar y Descargar ZIP';
                }
            });
        }
    })();

    // --- SCRIPT PARA LA PESTAÑA DE LISTA DE TIENDAS ---
    ;(() => {
        const scope = {};
        window.tiendasScript = scope; 

        let sucursalesData = [], filteredSucursales = [], postalCodeCoordinates = {};
        let selectedExcelFile = null, selectedJsonFile = null;

        const ui = {
            excelFileInput: document.getElementById('excelFileTiendas'),
            loadDataButton: document.getElementById('loadDataButtonTiendas'),
            jsonFileInput: document.getElementById('jsonFileTiendas'),
            loadJsonButton: document.getElementById('loadJsonButtonTiendas'),
            excelFileLabel : document.getElementById('excelFileTiendasLabel'),
            jsonFileLabel : document.getElementById('jsonFileTiendasLabel'),
            sucursalGridContainer: document.getElementById('sucursalGridContainer'),
            nearestBranchesListElement: document.getElementById('nearestBranchesListTiendas'),
            loadingMessage: document.getElementById('loadingMessageTiendas'),
            jsonLoadingMessage: document.getElementById('jsonLoadingMessageTiendas'),
            errorMessage: document.getElementById('errorMessageTiendas'),
            infoMessage: document.getElementById('infoMessageTiendas'),
            postalCodeSearchInfoMessage: document.getElementById('postalCodeSearchInfoTiendas'),
            progressBarContainer: document.getElementById('progressBarContainerTiendas'),
            progressBar: document.getElementById('progressBarTiendas'),
            companySelectTiendas: document.getElementById('companySelectTiendas'),
            stateSearchInput: document.getElementById('stateSearchTiendas'),
            stateDatalist: document.getElementById('stateDatalistTiendas'),
            municipalitySearchInput: document.getElementById('municipalitySearchTiendas'),
            municipalityDatalist: document.getElementById('municipalityDatalistTiendas'),
            searchLocationButton: document.getElementById('searchLocationButtonTiendas'),
            clearFiltersButton: document.getElementById('clearFiltersButtonTiendas'),
            postalCodeSearchInput: document.getElementById('postalCodeSearchTiendas'),
            searchNearestButton: document.getElementById('searchNearestButtonTiendas'),
            mapIframe: document.getElementById('map-iframe-tiendas'),
            currentSucursalLegend: document.getElementById('currentSucursalLegendTiendas'),
            branchNameSearchInput: document.getElementById('branchNameSearchTiendas'),
        };
        
        scope.copySucursalData = function(sucursalId, buttonElement) {
            const sucursal = sucursalesData.find(s => s.id_excel === sucursalId);
            if(sucursal) {
                const textToCopy = `${sucursal.nombre}\n${sucursal.direccion}`;
                 const textArea = document.createElement("textarea");
                textArea.value = textToCopy;
                textArea.style.position = "fixed"; 
                document.body.appendChild(textArea);
                textArea.focus();
                textArea.select();
                try {
                    document.execCommand('copy');
                    const originalIcon = buttonElement.innerHTML;
                    buttonElement.innerHTML = '<i class="fas fa-check"></i>';
                    buttonElement.classList.add('bg-green-500');
                    setTimeout(() => {
                        buttonElement.innerHTML = originalIcon;
                        buttonElement.classList.remove('bg-green-500');
                    }, 2000);
                } catch (err) {
                    console.error('Fallback: No se pudo copiar', err);
                }
                document.body.removeChild(textArea);
            }
        }

        function displayMessage(element, message, type) {
            if (element) {
                element.textContent = message; show(element);
                const typeColors = { error: 'var(--danger-bg)', info: 'var(--highlight-color)', loading: 'var(--text-color)' };
                element.style.color = typeColors[type] || 'var(--text-color)';
            }
        }
        function clearMessages() { [ui.errorMessage, ui.infoMessage, ui.loadingMessage, ui.jsonLoadingMessage, ui.postalCodeSearchInfoMessage].forEach(el => { if(el) hide(el) }); }
        function updateProgressBar(percentage, text) {
            show(ui.progressBarContainer);
            ui.progressBar.style.width = percentage + '%';
            ui.progressBar.textContent = text || `${Math.round(percentage)}%`;
            if (percentage >= 100 || percentage <= 0) setTimeout(() => { hide(ui.progressBarContainer); ui.progressBar.style.width = '0%'; }, 2000);
        }
        
        function renderSucursalGrid(dataToRender, gridContainerElement) {
            if (!gridContainerElement) return;
            gridContainerElement.innerHTML = '';

            if (dataToRender.length === 0) {
                gridContainerElement.innerHTML = `<p class="col-span-full text-center opacity-70">No se encontraron sucursales.</p>`;
                if (ui.mapIframe) ui.mapIframe.src = "about:blank";
                return;
            }

            dataToRender.forEach(sucursal => {
                const card = document.createElement('div');
                card.className = 'summary-box p-3 flex flex-col justify-between text-xs cursor-pointer hover:bg-white/10 relative';
                card.setAttribute('data-sucursal-id', sucursal.id_excel);
                card.onclick = () => scope.showSucursalOnMap(sucursal.id_excel);

                const estatusLower = (sucursal.Estatus || '').toLowerCase();
                let statusStyle = '';
                if (estatusLower.includes("abiert")) {
                    statusStyle = 'background-color: rgba(74, 222, 128, 0.15); color: #86EFAC;';
                } else if (estatusLower.includes("cerrad")) {
                    statusStyle = 'background-color: rgba(239, 68, 68, 0.15); color: #FCA5A5;';
                } else {
                    statusStyle = 'background-color: rgba(0, 0, 0, 0.5); color: #000;';
                }

                const distanceText = sucursal.distance ? `${sucursal.distance.toFixed(2)} km` : 'N/A';
                
                card.innerHTML = `
                    <button onclick="event.stopPropagation(); window.copySucursalData('${sucursal.id_excel}', this)" class="absolute top-2 right-2 btn btn-small !rounded-full w-8 h-8 p-0 flex items-center justify-center"><i class="fas fa-copy"></i></button>
                    <div>
                        <p class="font-bold pr-8">${sucursal.nombre}</p>
                        <p class="text-xs opacity-80 mt-1">${sucursal.direccion}</p>
                    </div>
                    <div class="mt-2 pt-2 border-t border-white/10 text-xs">
                        <p><strong>Estatus:</strong> <span class="font-bold px-2 py-1 rounded" style="${statusStyle}">${sucursal.Estatus}</span></p>
                        <p><strong>DAT:</strong> ${sucursal.DatActivo}</p>
                        <p><strong>Distancia:</strong> ${distanceText}</p>
                    </div>
                `;
                gridContainerElement.appendChild(card);
            });

            if (dataToRender.length > 0) {
                scope.showSucursalOnMap(dataToRender[0].id_excel);
            }
        }
        
        function renderUniqueCompaniesList(companyCounts) {
            const select = ui.companySelectTiendas;
            if(!select) return;
            select.innerHTML = '<option value="">Todas las empresas</option>';
            if (Object.keys(companyCounts).length === 0) return;

            const sortedCompanies = Object.keys(companyCounts).sort();
            sortedCompanies.forEach(companyName => {
                const option = document.createElement("option");
                option.value = companyName;
                option.textContent = `${companyName} (${companyCounts[companyName]})`;
                select.appendChild(option);
            });
        }
        
        function populateDatalists() {
            if(!ui.stateDatalist || !ui.municipalityDatalist) return;
            const uniqueStates = [...new Set(sucursalesData.map(s => s.estado).filter(Boolean))].sort();
            ui.stateDatalist.innerHTML = uniqueStates.map(state => `<option value="${state}"></option>`).join('');
            const selectedState = ui.stateSearchInput.value.toLowerCase().trim();
            if (selectedState) {
                const municipalitiesInState = [...new Set(sucursalesData.filter(s => s.estado && s.estado.toLowerCase() === selectedState).map(s => s.municipio).filter(Boolean))].sort();
                ui.municipalityDatalist.innerHTML = municipalitiesInState.map(m => `<option value="${m}"></option>`).join('');
            } else { ui.municipalityDatalist.innerHTML = ''; }
        }
        
        function highlightGridCard(id) {
            if(!ui.sucursalGridContainer) return;
            ui.sucursalGridContainer.querySelectorAll('div[data-sucursal-id]').forEach(div => div.classList.remove('bg-white/20'));
            const selectedCard = ui.sucursalGridContainer.querySelector(`div[data-sucursal-id="${id}"]`);
            if (selectedCard) { 
                selectedCard.classList.add('bg-white/20'); 
                selectedCard.scrollIntoView({ behavior: 'smooth', block: 'nearest' }); 
            }
        }

        scope.showSucursalOnMap = function (id_excel) {
            const sucursal = sucursalesData.find(s => s.id_excel === id_excel);
            if (sucursal && ui.mapIframe) {
                if (sucursal.lat && sucursal.lng) { ui.mapIframe.src = `https://maps.google.com/maps?q=${sucursal.lat},${sucursal.lng}&ll=${sucursal.lat},${sucursal.lng}&z=15&output=embed`; ui.currentSucursalLegend.textContent = `Mostrando (coords): ${sucursal.nombre}`; }
                else if (sucursal.direccion) { ui.mapIframe.src = `https://maps.google.com/maps?q=${encodeURIComponent(sucursal.direccion)}&output=embed`; ui.currentSucursalLegend.textContent = `Mostrando (direccion): ${sucursal.nombre}`; }
                else { displayMessage(ui.errorMessage, 'Coordenadas o direccion no disponible.', 'error'); return; }
                show(ui.currentSucursalLegend);
                highlightGridCard(id_excel);
            }
        }
        
        async function handleLoadExcelData() {
            if (!selectedExcelFile) return;
            clearMessages(); displayMessage(ui.loadingMessage, 'Procesando Excel...', 'loading'); setControlsDisabled(true); updateProgressBar(10, 'Leyendo...');
            try {
                const data = await selectedExcelFile.arrayBuffer();
                const workbook = XLSX.read(data);
                const json = XLSX.utils.sheet_to_json(workbook.Sheets[workbook.SheetNames[0]]);
                updateProgressBar(50, 'Procesando...');
                processExcelData(json);
                if (Object.keys(postalCodeCoordinates).length > 0) enrichSucursalesDataWithCoords();
                filteredSucursales = [...sucursalesData];
                renderSucursalGrid(filteredSucursales, ui.sucursalGridContainer);
                const companyCounts = sucursalesData.reduce((acc, s) => {
                    acc[s.empresa] = (acc[s.empresa] || 0) + 1;
                    return acc;
                }, {});
                renderUniqueCompaniesList(companyCounts);
                populateDatalists(); updateProgressBar(100, '¡Excel Cargado!');
                displayMessage(ui.loadingMessage, `${sucursalesData.length} sucursales cargadas.`, 'info');
            } catch (err) { displayMessage(ui.errorMessage, `Error al leer Excel: ${err.message}`, 'error'); updateProgressBar(0, 'Error');
            } finally { setControlsDisabled(false); }
        }
        
        function processExcelData(data) {
            const requiredColumns = ["NOMBRE DAT", "Nombre sucursal", "Calle", "Código Postal", "Ciudad/Municipio", "Estado"];
            if(!data[0] || requiredColumns.some(col => !(col in data[0]))) throw new Error(`Columnas requeridas faltantes: ${requiredColumns.join(', ')}`);
            sucursalesData = data.map((row, i) => ({
                id_excel: `excel_${i}`,
                nombre: `${String(row["NOMBRE DAT"] || "N/A").trim()} - ${String(row["Nombre sucursal"] || "N/A").trim()}`,
                DatActivo: String(row["Dat Activo"] || 'N/A').toLowerCase().trim(),
                Estatus: String(row["ESTATUS SUCURSAL"] || 'N/A').toLowerCase().trim(),
                empresa: String(row["NOMBRE DAT"] || "N/A").trim(),
                direccion: [row["Calle"], row["Número ext."], row["Colonia"], row["Código Postal"], row["Ciudad/Municipio"], row["Estado"]].filter(Boolean).map(String).join(', '),
                codigoPostal: String(row["Código Postal"] || '').trim(),
                estado: String(row["Estado"] || '').trim(),
                municipio: String(row["Ciudad/Municipio"] || '').trim(),
                lat: null, lng: null
            }));
        }

        async function handleLoadJsonData() {
            if (!selectedJsonFile) return;
            displayMessage(ui.jsonLoadingMessage, 'Cargando JSON...', 'loading'); setControlsDisabled(true);
            try {
                postalCodeCoordinates = JSON.parse(await selectedJsonFile.text());
                if (sucursalesData.length > 0) { enrichSucursalesDataWithCoords(); renderSucursalGrid(filteredSucursales, ui.sucursalGridContainer); }
                displayMessage(ui.jsonLoadingMessage, `${Object.keys(postalCodeCoordinates).length} coordenadas cargadas.`, 'info');
            } catch (err) { displayMessage(ui.errorMessage, `Error al leer JSON: ${err.message}`, 'error');
            } finally { setControlsDisabled(false); }
        }

        function enrichSucursalesDataWithCoords() {
            if (Object.keys(postalCodeCoordinates).length === 0) return;
            sucursalesData.forEach(sucursal => {
                const cpData = postalCodeCoordinates[sucursal.codigoPostal];
                if (cpData) { sucursal.lat = cpData.lat; sucursal.lng = cpData.lng; }
            });
        }
        
        function filterAndDisplay() {
            clearMessages();
            const companyTerm = ui.companySelectTiendas.value.toLowerCase().trim();
            const stateTerm = ui.stateSearchInput.value.toLowerCase().trim();
            const municipalityTerm = ui.municipalitySearchInput.value.toLowerCase().trim();
            const branchNameTerm = ui.branchNameSearchInput.value.toLowerCase().trim();
            
            filteredSucursales = sucursalesData.filter(s =>
                (!companyTerm || (s.empresa || "").toLowerCase() === companyTerm) &&
                (!stateTerm || (s.estado || "").toLowerCase() === stateTerm) &&
                (!municipalityTerm || (s.municipio || "").toLowerCase().includes(municipalityTerm)) &&
                (!branchNameTerm || (s.nombre || "").toLowerCase().includes(branchNameTerm))
            );
            renderSucursalGrid(filteredSucursales, ui.sucursalGridContainer);
            displayMessage(ui.infoMessage, `Mostrando ${filteredSucursales.length} sucursales.`, 'info');
        }
        
        function calculateDistance(lat1, lon1, lat2, lon2) {
            if ([lat1, lon1, lat2, lon2].some(v => v == null)) return Infinity;
            const R = 6371; const dLat = (lat2 - lat1) * Math.PI / 180; const dLon = (lon2 - lon1) * Math.PI / 180;
            const a = Math.sin(dLat / 2) * Math.sin(dLat / 2) + Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) * Math.sin(dLon / 2) * Math.sin(dLon / 2);
            return R * 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
        }

        function handlePostalCodeSearch() {
            clearMessages();
            const searchedCP = ui.postalCodeSearchInput.value.trim();
            if (!searchedCP) { displayMessage(ui.postalCodeSearchInfoMessage, 'Ingresa un codigo postal.', 'error'); return; }
            const targetCoords = postalCodeCoordinates[searchedCP];
            if (!targetCoords || !targetCoords.lat || !targetCoords.lng) { displayMessage(ui.postalCodeSearchInfoMessage, `Codigo postal ${searchedCP} no encontrado.`, 'error'); return; }
            const companyTerm = ui.companySelectTiendas.value.toLowerCase().trim();
            const branchesToSearch = companyTerm ? sucursalesData.filter(s => (s.empresa || "").toLowerCase() === companyTerm) : sucursalesData;
            const nearest10 = branchesToSearch.map(sucursal => ({ ...sucursal, distance: calculateDistance(targetCoords.lat, targetCoords.lng, sucursal.lat, sucursal.lng) })).filter(s => s.distance !== Infinity).sort((a, b) => a.distance - b.distance).slice(0, 10);
            renderSucursalGrid(nearest10, ui.sucursalGridContainer);
            displayMessage(ui.postalCodeSearchInfoMessage, `Mostrando las ${nearest10.length} sucursales mas cercanas a ${searchedCP}.`, 'info');
        }
        
        function setControlsDisabled(disabled) {
            [ui.loadDataButton, ui.loadJsonButton, ui.searchLocationButton, ui.clearFiltersButton, ui.searchNearestButton, ui.companySelectTiendas, ui.stateSearchInput, ui.municipalitySearchInput, ui.postalCodeSearchInput, ui.excelFileInput, ui.jsonFileInput].forEach(el => { if(el) el.disabled = disabled });
            if (!disabled) {
                if(ui.loadDataButton) ui.loadDataButton.disabled = !selectedExcelFile; 
                if(ui.loadJsonButton) ui.loadJsonButton.disabled = !selectedJsonFile;
                if(ui.searchNearestButton) ui.searchNearestButton.disabled = !Object.keys(postalCodeCoordinates).length || !sucursalesData.length;
            }
        }
        
        if(ui.excelFileInput) {
            ui.excelFileInput.addEventListener('change', (e) => { 
                selectedExcelFile = e.target.files[0]; 
                ui.loadDataButton.disabled = !selectedExcelFile; 
                if(selectedExcelFile) {
                    ui.excelFileLabel.textContent = selectedExcelFile.name;
                    displayMessage(ui.loadingMessage, `Archivo listo: ${selectedExcelFile.name}`, 'info');
                } else {
                    ui.excelFileLabel.textContent = 'Seleccionar Archivo';
                }
            });
        }
        if(ui.jsonFileInput) {
             ui.jsonFileInput.addEventListener('change', (e) => { 
                selectedJsonFile = e.target.files[0]; 
                ui.loadJsonButton.disabled = !selectedJsonFile; 
                if(selectedJsonFile) {
                    ui.jsonFileLabel.textContent = selectedJsonFile.name;
                    displayMessage(ui.jsonLoadingMessage, `Archivo listo: ${selectedJsonFile.name}`, 'info');
                } else {
                     ui.jsonFileLabel.textContent = 'Seleccionar Archivo';
                }
            });
        }
        if(ui.loadDataButton) ui.loadDataButton.addEventListener('click', handleLoadExcelData);
        if(ui.loadJsonButton) ui.loadJsonButton.addEventListener('click', handleLoadJsonData);
        if(ui.companySelectTiendas) ui.companySelectTiendas.addEventListener('change', filterAndDisplay);
        if(ui.searchLocationButton) ui.searchLocationButton.addEventListener('click', filterAndDisplay);
        if(ui.stateSearchInput) ui.stateSearchInput.addEventListener('input', populateDatalists);
        if(ui.clearFiltersButton) ui.clearFiltersButton.addEventListener('click', () => {
            if(ui.companySelectTiendas) ui.companySelectTiendas.value = '';
            if(ui.stateSearchInput) ui.stateSearchInput.value = '';
            if(ui.municipalitySearchInput) ui.municipalitySearchInput.value = '';
            if(ui.branchNameSearchInput) ui.branchNameSearchInput.value = '';
            filterAndDisplay();
        });
        if(ui.branchNameSearchInput) ui.branchNameSearchInput.addEventListener('input', filterAndDisplay);
        if(ui.searchNearestButton) ui.searchNearestButton.addEventListener('click', handlePostalCodeSearch);
        displayMessage(ui.infoMessage, 'Carga los archivos para comenzar.', 'info');
    })();

    // --- SCRIPT PARA LA PESTAÑA DEL ANALIZADOR DE DATOS ---
    ;(() => {
        let excelData = [];
        let currentResults = [];
        let sortState = { column: null, direction: 'asc' };

        const SPECIFIC_HEADERS = ['Fecha', 'Fol_Sol', 'TAG', 'Marca', 'Modelo', 'IMEI', 'N" Sis', 'Cliente', 'Agente venta', 'Dis venta', 'Distribuidor', 'Tienda', 'Accesorios', 'Entrega'];
        const DETAIL_HEADER = ['DETALLE TOTAL'];
        const SEARCHABLE_COLUMNS = ['Fecha', 'Fol_Sol', 'TAG', 'IMEI', 'Cliente', 'Agente venta', 'Dis venta', 'Distribuidor', 'Tienda'];

        const fileInput = document.getElementById('file-input-analyzer');
        const searchInput = document.getElementById('search-input-analyzer');
        const searchColumnSelect = document.getElementById('search-column-analyzer');
        const searchSpecificButton = document.getElementById('search-specific-button-analyzer');
        const searchDetailButton = document.getElementById('search-detail-button-analyzer');
        const fileInfo = document.getElementById('file-info-analyzer');
        const statusDiv = document.getElementById('status-analyzer');
        const resultsTable = document.getElementById('results-table-analyzer');
        const tableHeaders = document.getElementById('table-headers-analyzer');
        const tableBody = document.getElementById('table-body-analyzer');
        const progressContainer = document.getElementById('progress-container-analyzer');
        const progressBar = document.getElementById('progress-bar-analyzer');
        const downloadButton = document.getElementById('download-button-analyzer');


        fileInput.addEventListener('change', handleFile);

        function handleFile(event) {
            const file = event.target.files[0];
            if (!file) return;

            fileInfo.textContent = `Cargando: ${file.name}...`;
            clearResults();

            progressBar.style.width = '0%';
            progressBar.textContent = '';
            show(progressContainer);

            const reader = new FileReader();

            reader.onprogress = function(e) {
                if (e.lengthComputable) {
                    const percentLoaded = Math.round((e.loaded / e.total) * 100);
                    progressBar.style.width = percentLoaded + '%';
                    progressBar.textContent = percentLoaded + '%';
                }
            };

            reader.onload = function(e) {
                progressBar.style.width = '100%';
                progressBar.textContent = '100%';
                
                try {
                    const data = new Uint8Array(e.target.result);
                    const workbook = XLSX.read(data, { type: 'array' });
                    const firstSheetName = workbook.SheetNames[0];
                    const worksheet = workbook.Sheets[firstSheetName];
                    excelData = XLSX.utils.sheet_to_json(worksheet);

                    if (excelData.length > 0) {
                        searchInput.disabled = false;
                        searchSpecificButton.disabled = false;
                        searchDetailButton.disabled = false;
                        searchColumnSelect.disabled = false;
                        fileInfo.textContent = `${excelData.length} filas cargadas.`;
                        populateColumnSelect();
                    } else {
                        fileInfo.textContent = 'El archivo esta vacio.';
                    }
                } catch (error) {
                    fileInfo.textContent = 'Error al procesar el archivo.';
                    console.error(error);
                } finally {
                    setTimeout(() => {
                        hide(progressContainer);
                    }, 1000);
                }
            };
            
            reader.onerror = function() {
                fileInfo.textContent = 'Ocurrio un error al leer el archivo.';
            };

            reader.readAsArrayBuffer(file);
        }

        function populateColumnSelect(){
            searchColumnSelect.innerHTML = '<option value="all">Todas las columnas</option>';
            SEARCHABLE_COLUMNS.forEach(col => {
                const option = document.createElement('option');
                option.value = col;
                option.textContent = col;
                searchColumnSelect.appendChild(option);
            });
        }
        
        function findMatchingRows() {
            const searchTerm = searchInput.value.trim().toLowerCase();
            const selectedColumn = searchColumnSelect.value;

            if (searchTerm === '') {
                statusDiv.textContent = 'Por favor, introduce un termino de busqueda.';
                return null;
            }
            if (excelData.length === 0) {
                statusDiv.textContent = 'No hay datos cargados para buscar.';
                return null;
            }

            if (selectedColumn === 'all') {
                 return excelData.filter(row => SEARCHABLE_COLUMNS.some(column => {
                    const cellValue = row[column];
                    const cellString = cellValue != null ? cellValue.toString().toLowerCase() : '';
                    return cellString === searchTerm;
                }));
            } else {
                 return excelData.filter(row => {
                    const cellValue = row[selectedColumn];
                    const cellString = cellValue != null ? cellValue.toString().toLowerCase() : '';
                    return cellString === searchTerm;
                 });
            }
        }
        
        function drawTable(results, headersToShow) {
            clearResults();
            drawHeaders(headersToShow);
            drawBody(results, headersToShow);

            if (results.length === 0) {
                statusDiv.textContent = 'No se encontraron resultados para tu busqueda.';
                downloadButton.disabled = true;
            } else {
                statusDiv.textContent = `Se encontraron ${results.length} resultado(s).`;
                downloadButton.disabled = headersToShow !== SPECIFIC_HEADERS;
            }
        }
        
        function drawHeaders(headersToShow) {
            const headerRow = document.createElement('tr');
            headersToShow.forEach(header => {
                const th = document.createElement('th');
                th.className = 'table-cell sortable-header';
                th.textContent = header;
                th.onclick = () => sortTable(header, headersToShow);
                
                const icon = document.createElement('span');
                icon.className = 'sort-icon';
                if (sortState.column === header) {
                    icon.textContent = sortState.direction === 'asc' ? ' ▲' : ' ▼';
                }
                th.appendChild(icon);
                headerRow.appendChild(th);
            });

            if (headersToShow === DETAIL_HEADER) {
                const actionTh = document.createElement('th');
                actionTh.textContent = "Accion";
                actionTh.className = 'table-cell';
                headerRow.appendChild(actionTh);
            }
            
            tableHeaders.appendChild(headerRow);
        }

        function drawBody(results, headersToShow) {
             results.forEach(row => {
                const tr = document.createElement('tr');
                headersToShow.forEach(header => {
                    const td = document.createElement('td');
                    td.className = 'table-cell';
                    td.textContent = row[header] || ''; 
                    tr.appendChild(td);
                });

                if (headersToShow === DETAIL_HEADER) {
                    const buttonTd = document.createElement('td');
                    buttonTd.className = 'table-cell';
                    const copyButton = document.createElement('button');
                    copyButton.textContent = 'Copiar';
                    copyButton.className = 'btn btn-small';
                    copyButton.onclick = () => copyToClipboard(row[DETAIL_HEADER[0]] || '', copyButton);
                    buttonTd.appendChild(copyButton);
                    tr.appendChild(buttonTd);
                }
                
                tableBody.appendChild(tr);
            });
        }
        
        function sortTable(columnName, headersToShow) {
            if (sortState.column === columnName) {
                sortState.direction = sortState.direction === 'asc' ? 'desc' : 'asc';
            } else {
                sortState.column = columnName;
                sortState.direction = 'asc';
            }

            currentResults.sort((a, b) => {
                const valA = a[sortState.column] || '';
                const valB = b[sortState.column] || '';
                if (valA < valB) return -1;
                if (valA > valB) return 1;
                return 0;
            });

            if (sortState.direction === 'desc') {
                currentResults.reverse();
            }

            drawTable(currentResults, headersToShow);
        }
        
        function clearResults() {
            statusDiv.textContent = '';
            tableHeaders.innerHTML = '';
            tableBody.innerHTML = '';
            downloadButton.disabled = true;
        }

        function copyToClipboard(text, buttonElement) {
            const textArea = document.createElement("textarea");
            textArea.value = text;
            textArea.style.position = "fixed"; 
            document.body.appendChild(textArea);
            textArea.focus();
            textArea.select();
            try {
                document.execCommand('copy');
                buttonElement.textContent = '¡Copiado!';
                setTimeout(() => buttonElement.textContent = 'Copiar', 2000);
            } catch (err) {
                console.error('Error al copiar:', err);
                buttonElement.textContent = 'Error';
            }
            document.body.removeChild(textArea);
        }

        function downloadResults() {
            if (currentResults.length === 0) {
                alert('No hay resultados para descargar.');
                return;
            }

            const headers = SPECIFIC_HEADERS;
            let csvContent = headers.join(",") + "\n";

            currentResults.forEach(row => {
                const values = headers.map(header => {
                    let cell = row[header] ? row[header].toString() : '';
                    if (cell.includes(',')) {
                        cell = `"${cell.replace(/"/g, '""')}"`;
                    }
                    return cell;
                });
                csvContent += values.join(",") + "\n";
            });

            const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement("a");
            const url = URL.createObjectURL(blob);
            link.setAttribute("href", url);
            link.setAttribute("download", "resultados_busqueda.csv");
            link.style.visibility = 'hidden';
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }

        function searchSpecificData() {
            resultsTable.classList.add('no-wrap');
            currentResults = findMatchingRows() || [];
            sortState = { column: null, direction: 'asc' };
            drawTable(currentResults, SPECIFIC_HEADERS);
        }

        function searchDetailData() {
            resultsTable.classList.remove('no-wrap');
            currentResults = findMatchingRows() || [];
            sortState = { column: null, direction: 'asc' };
            drawTable(currentResults, DETAIL_HEADER);
        }

        searchSpecificButton.addEventListener('click', searchSpecificData);
        searchDetailButton.addEventListener('click', searchDetailData);
        downloadButton.addEventListener('click', downloadResults);
        
        searchInput.addEventListener('keyup', e => {
            if (e.key === 'Enter') searchSpecificData();
        });

    })();

});
</script>
</body>
</html>
