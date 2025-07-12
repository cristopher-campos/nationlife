
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NationLife: Simulador de País</title>
    <!-- Carga de Tailwind CSS para estilos rápidos y responsivos -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Fuente Inter para una mejor legibilidad */
        body {
            font-family: 'Inter', sans-serif;
            background: linear-gradient(135deg, #1a202c 0%, #2d3748 50%, #1a202c 100%); /* Nuevo degradado de fondo */
            color: #e2e8f0; /* Texto claro por defecto */
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 1rem; /* Añadir padding para pantallas pequeñas */
            box-sizing: border-box; /* Incluir padding en el ancho total */
            overflow-y: auto; /* Permitir scroll en el cuerpo si es necesario */
        }
        .game-container {
            background-color: #2d3748; /* Contenedor del juego más claro */
            background-image: linear-gradient(to bottom right, #2d3748, #1a202c); /* Degradado sutil */
            border-radius: 1.5rem; /* Bordes redondeados más pronunciados */
            box-shadow: 0 15px 30px rgba(0, 0, 0, 0.7), 0 0 80px rgba(66, 153, 225, 0.3); /* Sombra más profunda con resplandor azul */
            padding: 2.5rem; /* Más padding interno */
            max-width: 95%; /* Ancho máximo para responsividad */
            width: 800px; /* Ancho fijo para escritorio */
            display: flex;
            flex-direction: column;
            gap: 2rem; /* Mayor espacio entre secciones */
            border: 1px solid rgba(74, 85, 104, 0.4); /* Borde sutil */
            position: relative; /* Necesario para el footer fijo */
            min-height: 80vh; /* Asegura que el contenedor sea lo suficientemente alto para el footer */
            animation: fadeIn 1s ease-out; /* Animación de entrada */
            margin-bottom: 70px; /* Espacio para el footer fijo */
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .section-card {
            background-color: #2d3748; /* Fondo para las tarjetas de sección */
            background-image: linear-gradient(to bottom right, #2d3748, #222c3b); /* Degradado más oscuro para tarjetas */
            border-radius: 1rem; /* Bordes redondeados */
            padding: 1.5rem;
            box-shadow: 0 8px 20px rgba(0, 0, 0, 0.5), 0 0 30px rgba(100, 100, 200, 0.15); /* Sombra para tarjetas con resplandor */
            border: 1px solid rgba(74, 85, 104, 0.3);
        }
        .stat-bar-container {
            background-color: #4a5568;
            border-radius: 0.75rem;
            overflow: hidden;
            height: 1.5rem;
            box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.4); /* Sombra interna para efecto de profundidad */
        }
        .stat-bar {
            height: 100%;
            background-image: linear-gradient(to right, #48bb78, #38a169); /* Degradado para barras */
            transition: width 0.6s ease-in-out, background-image 0.6s ease-in-out;
        }
        /* Colores para barras bajas */
        .stat-bar.low-economy { background-image: linear-gradient(to right, #ef4444, #dc2626); }
        .stat-bar.low-happiness { background-image: linear-gradient(to right, #f6ad55, #ed8936); } /* Naranja para felicidad baja */
        .stat-bar.low-stability { background-image: linear-gradient(to right, #f56565, #e53e3e); } /* Rojo para estabilidad muy baja */
        .stat-bar.low-relations { background-image: linear-gradient(to right, #ecc94b, #d69e2e); } /* Amarillo para relaciones bajas */
        .stat-bar.low-efficiency { background-image: linear-gradient(to right, #a0aec0, #718096); } /* Gris para eficiencia baja */


        .button-option {
            background-image: linear-gradient(to right, #4299e1, #3182ce); /* Degradado para botones */
            color: white;
            padding: 0.85rem 1.75rem; /* Más padding */
            border-radius: 0.75rem;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease; /* Transición para todos los cambios */
            text-align: center;
            border: none; /* Eliminar borde por defecto */
            box-shadow: 0 8px 16px rgba(0, 0, 0, 0.6); /* Sombra más pronunciada para botones */
            font-size: 1.1rem; /* Fuente un poco más grande */
            display: flex; /* Para centrar contenido y emojis */
            align-items: center;
            justify-content: center;
            gap: 0.5rem; /* Espacio entre texto y emoji */
            position: relative; /* Para efectos de brillo */
            overflow: hidden; /* Para contener el brillo */
        }
        .button-option:hover {
            background-image: linear-gradient(to right, #3182ce, #2b6cb0); /* Degradado más oscuro al pasar el ratón */
            transform: translateY(-5px) scale(1.03); /* Efecto de "levantar" y crecer ligeramente más */
            box-shadow: 0 12px 25px rgba(0, 0, 0, 0.7); /* Sombra más grande al pasar el ratón */
            filter: brightness(1.1); /* Ligero brillo */
        }
        .button-option:active {
            transform: translateY(0) scale(0.97); /* Efecto de "presionar" */
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.4);
            filter: brightness(0.9); /* Ligera oscuridad al presionar */
        }
        .message-box {
            background-color: #2d3748;
            background-image: linear-gradient(to bottom right, #2d3748, #1a202c);
            border: 1px solid #4a5568;
            border-radius: 1rem; /* Bordes más redondeados */
            padding: 1.5rem; /* Más padding */
            margin-top: 1.5rem;
            display: none; /* Oculto por defecto */
            text-align: center;
            color: #e2e8f0;
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.5); /* Sombra más pronunciada */
            font-size: 1.1rem;
            z-index: 1000; /* Asegura que esté por encima de todo */
            position: fixed; /* Fijo en la pantalla */
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%); /* Centrar */
            width: 90%;
            max-width: 400px;
        }
        /* Estilos para el select de países */
        .country-select {
            width: 100%;
            padding: 0.75rem;
            border-radius: 0.5rem;
            background-color: #4a5568;
            color: #e2e8f0;
            border: 1px solid #6b7280;
            font-size: 1rem;
            margin-bottom: 1.5rem;
            appearance: none; /* Elimina el estilo por defecto del select */
            -webkit-appearance: none;
            -moz-appearance: none;
            background-image: url('data:image/svg+xml;charset=US-ASCII,%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20viewBox%3D%220%200%2020%2020%22%20fill%3D%22%23e2e8f0%22%3E%3Cpath%20fill-rule%3D%22evenodd%22%20d%3D%22M5.293%207.293a1%201%200%20011.414%200L10%2010.586l3.293-3.293a1%201%200%20111.414%201.414l-4%204a1%201%200%2001-1.414%200l-4-4a1%201%200%20010-1.414z%22%20clip-rule%3D%22evenodd%22%3E%3C%2Fpath%3E%3C%2Fsvg%3E');
            background-repeat: no-repeat;
            background-position: right 0.75rem center;
            background-size: 1.5em 1.5em;
        }
        /* Estilos para el campo de búsqueda */
        .search-input {
            width: 100%;
            padding: 0.75rem;
            border-radius: 0.5rem;
            background-color: #4a5568;
            color: #e2e8f0;
            border: 1px solid #6b7280;
            font-size: 1rem;
            margin-bottom: 1rem; /* Espacio entre el input y el select */
        }
        .search-input::placeholder {
            color: #a0aec0;
        }
        /* Estilos para la bandera emoji */
        .country-flag-emoji {
            font-size: 3rem; /* Tamaño grande para el emoji */
            line-height: 1; /* Asegura que el emoji no tenga espacio extra */
            margin-bottom: 1rem;
        }
        /* Footer del juego */
        .game-footer {
            position: fixed;
            bottom: 0;
            left: 0;
            width: 100%;
            background-color: #2d3748;
            background-image: linear-gradient(to right, #2d3748, #1a202c);
            padding: 0.75rem 1rem;
            box-shadow: 0 -5px 15px rgba(0, 0, 0, 0.5);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 500; /* Por encima del contenido, pero debajo de los modales */
            border-top-left-radius: 1rem;
            border-top-right-radius: 1rem;
            flex-wrap: wrap; /* Permite que los botones se envuelvan */
        }
        .footer-button {
            background-image: linear-gradient(to right, #63b3ed, #4299e1);
            color: white;
            padding: 0.6rem 1.2rem; /* Reducido el padding */
            border-radius: 0.75rem;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            gap: 0.5rem;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
            font-size: 1rem; /* Reducido el tamaño de fuente */
            margin: 0.2rem; /* Reducido el espacio entre botones */
            position: relative; /* Para efectos de brillo */
            overflow: hidden; /* Para contener el brillo */
        }
        .footer-button:hover {
            background-image: linear-gradient(to right, #4299e1, #3182ce);
            transform: translateY(-3px);
            box-shadow: 0 6px 12px rgba(0, 0, 0, 0.4);
            filter: brightness(1.1); /* Ligero brillo */
        }
        .footer-button:active {
            transform: translateY(0);
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
            filter: brightness(0.9); /* Ligera oscuridad al presionar */
        }

        /* Estilos para los modales */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.7); /* Fondo oscuro semitransparente */
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 900; /* Por encima del footer */
            opacity: 0;
            visibility: hidden;
            transition: opacity 0.3s ease, visibility 0.3s ease;
        }
        .modal-overlay.show {
            opacity: 1;
            visibility: visible;
        }
        .modal-content {
            background-color: #2d3748;
            background-image: linear-gradient(to bottom right, #2d3748, #1a202c);
            border-radius: 1.5rem;
            box-shadow: 0 15px 25px rgba(0, 0, 0, 0.6), 0 0 60px rgba(66, 153, 225, 0.3);
            padding: 2rem;
            max-width: 90%;
            width: 600px; /* Ancho ajustable para el modal */
            max-height: 90vh; /* Altura máxima para evitar desbordamiento */
            overflow-y: auto; /* Scroll si el contenido es demasiado largo */
            position: relative;
            transform: translateY(20px); /* Efecto de entrada */
            transition: transform 0.3s ease;
        }
        .modal-overlay.show .modal-content {
            transform: translateY(0);
        }
        .modal-close-button {
            position: absolute;
            top: 1rem;
            right: 1rem;
            background: none;
            border: none;
            font-size: 1.5rem;
            color: #e2e8f0;
            cursor: pointer;
            transition: color 0.2s ease;
        }
        .modal-close-button:hover {
            color: #ef4444;
        }

        /* Estilos para los inputs de porcentaje en el modal de fondos */
        .fund-input-group {
            display: flex;
            align-items: center;
            justify-content: space-between;
            gap: 1rem;
            margin-bottom: 0.75rem;
            background-color: #3a4455;
            padding: 0.75rem 1rem;
            border-radius: 0.75rem;
            border: 1px solid #4a5568;
        }
        .fund-input-group label {
            font-weight: bold;
            flex-grow: 1;
            color: #a0aec0;
            min-width: 120px; /* Asegura espacio para el texto */
        }
        .fund-input-group input[type="range"] {
            flex-grow: 1;
            -webkit-appearance: none; /* Eliminar estilos por defecto de WebKit */
            appearance: none;
            height: 8px;
            background: #6b7280;
            outline: none;
            border-radius: 5px;
            transition: opacity .2s;
            margin: 0 10px;
        }

        .fund-input-group input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            appearance: none;
            width: 20px;
            height: 20px;
            border-radius: 50%;
            background: #4299e1;
            cursor: pointer;
            box-shadow: 0 2px 5px rgba(0,0,0,0.3);
            transition: background 0.2s ease;
        }

        .fund-input-group input[type="range"]::-moz-range-thumb {
            width: 20px;
            height: 20px;
            border-radius: 50%;
            background: #4299e1;
            cursor: pointer;
            box-shadow: 0 2px 5px rgba(0,0,0,0.3);
            transition: background 0.2s ease;
        }

        .fund-input-group input[type="range"]:hover::-webkit-slider-thumb {
            background: #3182ce;
        }
        .fund-input-group input[type="range"]:hover::-moz-range-thumb {
            background: #3182ce;
        }

        .fund-input-group span.percentage-value {
            font-weight: bold;
            color: #e2e8f0;
            width: 40px; /* Ancho fijo para el porcentaje */
            text-align: right;
        }
        .total-allocated-display {
            font-size: 1.25rem;
            font-weight: bold;
            text-align: center;
            margin-top: 1.5rem;
            padding: 0.75rem;
            border-radius: 0.75rem;
            background-color: #3a4455;
            border: 1px solid #4a5568;
        }
        .total-allocated-display.error {
            color: #ef4444;
            border-color: #ef4444;
        }
        .total-allocated-display.success {
            color: #48bb78;
            border-color: #48bb78;
        }

        /* Estilos para los inputs de cantidad en el modal económico */
        .economic-action-group {
            display: flex;
            align-items: center;
            gap: 0.5rem;
            margin-bottom: 0.75rem;
        }
        .economic-action-group input[type="number"] {
            flex-grow: 1;
            padding: 0.5rem;
            border-radius: 0.5rem;
            background-color: #4a5568;
            color: #e2e8f0;
            border: 1px solid #6b7280;
            font-size: 0.9rem;
            max-width: 100px; /* Limitar ancho del input */
        }

        /* Confirmation Modal Specific Styles */
        .confirmation-modal-content {
            background-color: #2d3748;
            background-image: linear-gradient(to bottom right, #2d3748, #1a202c);
            border-radius: 1.5rem;
            box-shadow: 0 15px 25px rgba(0, 0, 0, 0.6), 0 0 60px rgba(66, 153, 225, 0.3);
            padding: 2rem;
            max-width: 90%;
            width: 500px;
            text-align: center;
            position: relative;
        }
        .confirmation-modal-content h3 {
            font-size: 1.75rem;
            font-weight: bold;
            color: #e2e8f0;
            margin-bottom: 1rem;
        }
        .confirmation-modal-content p {
            font-size: 1.1rem;
            color: #a0aec0;
            margin-bottom: 2rem;
            line-height: 1.6;
        }
        .confirmation-modal-content .button-group {
            display: flex;
            justify-content: center;
            gap: 1rem;
        }

        /* Audio controls styling */
        .audio-controls {
            display: flex;
            align-items: center;
            gap: 0.5rem;
            background-color: #3a4455;
            padding: 0.5rem 1rem;
            border-radius: 0.75rem;
            box-shadow: inset 0 2px 4px rgba(0,0,0,0.3);
            margin-top: 1rem; /* Espacio superior para separarlo de los botones */
        }
        .audio-controls button {
            background: none;
            border: none;
            color: #e2e8f0;
            font-size: 1.5rem;
            cursor: pointer;
            transition: color 0.2s ease;
        }
        .audio-controls button:hover {
            color: #a0aec0;
        }
        .audio-controls input[type="range"] {
            -webkit-appearance: none;
            appearance: none;
            width: 80px;
            height: 5px;
            background: #6b7280;
            outline: none;
            border-radius: 5px;
            cursor: pointer;
        }
        .audio-controls input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            appearance: none;
            width: 15px;
            height: 15px;
            border-radius: 50%;
            background: #4299e1;
            cursor: pointer;
            box-shadow: 0 1px 3px rgba(0,0,0,0.3);
        }
        .audio-controls input[type="range"]::-moz-range-thumb {
            width: 15px;
            height: 15px;
            border-radius: 50%;
            background: #4299e1;
            cursor: pointer;
            box-shadow: 0 1px 3px rgba(0,0,0,0.3);
        }

        /* News Feed Styles */
        .news-feed-section {
            max-height: 250px; /* Fixed height for scrolling */
            overflow-y: auto; /* Enable vertical scrolling */
            padding-right: 0.5rem; /* Space for scrollbar */
            scrollbar-width: thin; /* Firefox */
            scrollbar-color: #4a5568 #2d3748; /* Firefox */
        }
        .news-feed-section::-webkit-scrollbar {
            width: 8px; /* WebKit */
        }
        .news-feed-section::-webkit-scrollbar-track {
            background: #2d3748; /* WebKit */
            border-radius: 10px;
        }
        .news-feed-section::-webkit-scrollbar-thumb {
            background-color: #4a5568; /* WebKit */
            border-radius: 10px;
            border: 2px solid #2d3748;
        }

        .news-item {
            background-color: #3a4455;
            border-radius: 0.75rem;
            padding: 0.75rem 1rem;
            margin-bottom: 0.75rem;
            border: 1px solid #4a5568;
            display: flex;
            flex-direction: column;
            gap: 0.25rem;
            transition: background-color 0.3s ease;
        }
        .news-item:hover {
            background-color: #4a5568;
        }
        .news-item-title {
            font-weight: bold;
            color: #e2e8f0;
            font-size: 1rem;
        }
        .news-item-description {
            font-size: 0.9rem;
            color: #a0aec0;
        }
        .news-item-impact {
            font-size: 0.85rem;
            color: #cbd5e0;
            margin-top: 0.25rem;
            display: flex;
            flex-wrap: wrap;
            gap: 0.5rem;
        }
        .news-item-impact span {
            padding: 0.2rem 0.5rem;
            border-radius: 0.5rem;
            font-weight: bold;
        }
        .news-item-impact .positive { background-color: #2f855a; color: white; } /* Darker green */
        .news-item-impact .negative { background-color: #c53030; color: white; } /* Darker red */
        .news-item-impact .neutral { background-color: #4a5568; color: white; } /* Darker gray */

        /* Stat change animation */
        .stat-value-changed {
            animation: statChange 0.5s ease-out;
        }
        @keyframes statChange {
            0% { transform: scale(1); color: inherit; }
            50% { transform: scale(1.1); color: #63b3ed; } /* Bright blue flash */
            100% { transform: scale(1); color: inherit; }
        }

        /* Country Challenges Section */
        .challenges-section {
            margin-top: 1.5rem;
            padding: 1rem;
            background-color: #2d3748;
            border-radius: 1rem;
            box-shadow: inset 0 2px 4px rgba(0,0,0,0.3);
            border: 1px solid #4a5568;
        }
        .challenge-item {
            display: flex;
            align-items: center;
            gap: 0.75rem;
            margin-bottom: 0.5rem;
            font-size: 1rem;
            color: #ef4444; /* Red for challenges */
            font-weight: bold;
        }
        .challenge-item:last-child {
            margin-bottom: 0;
        }

        /* Ideology Modal Specific Styles */
        .ideology-card {
            background-color: #3a4455;
            border-radius: 0.75rem;
            padding: 1rem;
            margin-bottom: 1rem;
            border: 1px solid #4a5568;
            box-shadow: 0 4px 10px rgba(0,0,0,0.3);
            cursor: pointer;
            transition: all 0.2s ease;
        }
        .ideology-card:hover {
            background-color: #4a5568;
            transform: translateY(-2px);
            box-shadow: 0 6px 15px rgba(0,0,0,0.4);
        }
        .ideology-card.selected {
            border-color: #4299e1;
            box-shadow: 0 0 0 3px #4299e1, 0 6px 15px rgba(0,0,0,0.4);
        }
        .ideology-card h4 {
            font-size: 1.25rem;
            font-weight: bold;
            color: #e2e8f0;
            margin-bottom: 0.5rem;
        }
        .ideology-card p {
            font-size: 0.95rem;
            color: #a0aec0;
            margin-bottom: 0.75rem;
        }
        .ideology-card .impact-list {
            display: flex;
            flex-wrap: wrap;
            gap: 0.5rem;
            font-size: 0.8rem;
        }
        .ideology-card .impact-list span {
            padding: 0.1rem 0.4rem;
            border-radius: 0.4rem;
            font-weight: bold;
        }

        /* Military Modal Styles */
        .military-stat-display {
            font-size: 1.25rem;
            font-weight: bold;
            text-align: center;
            margin-bottom: 1.5rem;
            padding: 0.75rem;
            border-radius: 0.75rem;
            background-color: #3a4455;
            border: 1px solid #4a5568;
        }
        .military-action-group {
            display: flex;
            flex-direction: column;
            gap: 0.75rem;
            margin-bottom: 1rem;
            padding: 1rem;
            background-color: #2d3748;
            border-radius: 0.75rem;
            border: 1px solid #4a5568;
        }
        .military-action-group label {
            font-weight: bold;
            color: #a0aec0;
        }
        .military-action-group input[type="number"] {
            width: 100%;
            padding: 0.5rem;
            border-radius: 0.5rem;
            background-color: #4a5568;
            color: #e2e8f0;
            border: 1px solid #6b7280;
            font-size: 0.9rem;
        }
        .military-action-group .button-option {
            width: 100%;
            margin-top: 0.5rem;
        }


        /* Ajustes responsivos */
        @media (max-width: 768px) {
            .game-container {
                padding: 1rem;
                gap: 1rem;
                margin-bottom: 60px; /* Ajustar para el footer en móvil */
            }
            .section-card {
                padding: 1rem;
            }
            .button-option {
                padding: 0.75rem 1.25rem;
                font-size: 1rem;
            }
            .country-flag-emoji {
                font-size: 2.5rem;
            }
            .game-footer {
                padding: 0.5rem;
                flex-wrap: wrap; /* Permite que los botones se envuelvan en pantallas pequeñas */
            }
            .footer-button {
                padding: 0.5rem 0.8rem; /* Más pequeño en móvil */
                font-size: 0.9rem; /* Fuente más pequeña en móvil */
                margin: 0.15rem; /* Espacio entre botones en wrap */
            }
            .modal-content {
                padding: 1.5rem;
            }
            .fund-input-group {
                flex-direction: column;
                align-items: flex-start;
            }
            .fund-input-group label {
                margin-bottom: 0.25rem;
            }
            .fund-input-group input[type="range"] {
                width: 100%;
                margin: 0; /* Eliminar margen extra en móvil */
            }
            .fund-input-group span.percentage-value {
                width: auto; /* Ajustar ancho en móvil */
                text-align: left;
                margin-top: 0.25rem;
            }
            .economic-action-group {
                flex-direction: column;
                align-items: stretch;
            }
            .economic-action-group button {
                width: 100%;
            }
            .economic-action-group input[type="number"] {
                max-width: none;
                width: 100%;
            }
            .audio-controls {
                flex-direction: column;
                align-items: center;
                width: 100%;
            }
            .audio-controls input[type="range"] {
                width: 100%;
            }
        }
    </style>
</head>
<body>
    <div class="game-container">
        <!-- Pantalla Principal -->
        <div id="mainMenuScreen" class="flex flex-col items-center justify-center text-center">
            <h1 class="text-5xl font-extrabold text-blue-400 mb-8 rounded-xl p-3 bg-gray-800 shadow-xl border border-blue-600">
                NationLife
            </h1>
            <p class="text-xl text-gray-300 mb-10">El simulador de vida de un país</p>
            <div class="flex flex-col gap-6 w-full max-w-xs">
                <button id="btnChooseCountry" class="button-option text-2xl py-4">
                    <span class="mr-2">🌍</span> Elegir País
                </button>
                <button id="btnContinueGame" class="button-option text-2xl py-4">
                    <span class="mr-2">▶️</span> Continuar Partida
                </button>
            </div>
        </div>

        <!-- Pantalla de Selección de País (oculta por defecto) -->
        <div id="countrySelectionScreen" class="hidden flex-col items-center justify-center text-center">
            <h2 class="text-4xl font-extrabold text-blue-400 mb-8 rounded-xl p-3 bg-gray-800 shadow-xl border border-blue-600">
                Elige tu País
            </h2>
            <p class="text-lg text-gray-300 mb-6">Selecciona la nación que deseas gobernar:</p>
            <div class="w-full max-w-xs">
                <input type="text" id="countrySearchInput" class="search-input" placeholder="Buscar país...">
                <select id="countrySelect" class="country-select">
                    <!-- Las opciones se llenarán con JavaScript -->
                </select>
                <button id="btnConfirmCountry" class="button-option text-xl py-3 mt-4 w-full">
                    <span class="mr-2">✅</span> Confirmar Selección
                </button>
                <button id="btnBackToMain" class="button-option text-xl py-3 mt-4 w-full bg-gray-600 hover:bg-gray-700">
                    <span class="mr-2">⬅️</span> Volver al Menú
                </button>
            </div>
        </div>

        <!-- Pantalla del Juego (oculta por defecto) -->
        <div id="gameScreen" class="hidden flex-col gap-2 w-full pb-20"> <!-- Añadir padding-bottom para el footer -->
            <!-- Encabezado del juego -->
            <h1 class="text-4xl font-extrabold text-center text-blue-400 mb-4 rounded-xl p-3 bg-gray-800 shadow-xl border border-blue-600">
                NationLife: Simulador de País
            </h1>

            <div class="flex flex-col items-center mb-4">
                <span id="countryFlagEmoji" class="country-flag-emoji"></span>
                <p class="text-2xl font-bold text-blue-300">País: <span id="currentCountryDisplay"></span></p>
            </div>

            <p class="text-2xl font-bold mt-6 text-center text-white bg-gray-800 p-2 rounded-lg shadow-inner">
                Año: <span id="year">1</span>, Mes: <span id="month">1</span> <span class="ml-2">🗓️</span>
            </p>

            <!-- Sección de estadísticas generales en la pantalla principal -->
            <div class="stats-overview-section section-card text-center">
                <h2 class="text-2xl font-bold mb-4 text-gray-200">Estado Actual <span class="ml-2">✨</span></h2>
                <div class="grid grid-cols-2 sm:grid-cols-3 gap-4 text-lg">
                    <p class="text-yellow-300"><span class="mr-1">📈</span> Economía: <span id="economyValue">100</span>%</p>
                    <p class="text-pink-300"><span class="mr-1">😊</span> Felicidad: <span id="happinessValue">100</span>%</p>
                    <p class="text-red-300"><span class="mr-1">🛡️</span> Estabilidad: <span id="stabilityValue">100</span>%</p>
                    <p class="text-purple-300"><span class="mr-1">🤝</span> Relaciones: <span id="relationsValue">100</span>%</p>
                    <p class="text-green-300"><span class="mr-1">👥</span> Población: <span id="populationValue">0</span> M</p>
                    <p class="text-teal-300"><span class="mr-1">🔬</span> Tecnología: <span id="techDevelopmentValue">0</span>%</p>
                    <p class="text-blue-200"><span class="mr-1">🏅</span> Diplomacia: <span id="diplomaticScoreValue">0</span>%</p>
                    <p class="text-gray-400"><span class="mr-1">⚙️</span> Eficiencia: <span id="efficiencyValue">100</span>%</p>
                    <p class="text-orange-300 col-span-2 sm:col-span-1"><span class="mr-1">🏛️</span> Ideología: <span id="ideologyValue">Capitalismo</span></p>
                    <p class="text-red-500 col-span-2 sm:col-span-1"><span class="mr-1">💂</span> Ejército: <span id="armySizeValue">0</span>k</p>
                </div>
                <!-- Country Challenges Section -->
                <div id="countryChallengesSection" class="challenges-section hidden">
                    <h3 class="text-xl font-bold mb-2 text-red-300">Desafíos Actuales <span class="ml-2">⚠️</span></h3>
                    <div id="activeChallengesList" class="flex flex-col gap-2">
                        <!-- Challenges will be listed here -->
                    </div>
                </div>
            </div>

            <hr class="my-6 border-t-2 border-gray-600 rounded-full shadow-lg"> <!-- Línea divisoria -->

            <!-- Sección de eventos y decisiones -->
            <div class="event-section section-card">
                <h2 class="text-2xl font-bold mb-4 text-center text-gray-200">Evento Actual</h2>
                <p id="eventText" class="text-lg mb-6 text-center leading-relaxed">
                    ¡Bienvenido a NationLife! Eres el nuevo líder de tu país. Tu primera tarea es guiarlo hacia la prosperidad.
                </p>
                <div id="optionsContainer" class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <!-- Opciones de decisión (se generarán con JS) -->
                    <button id="option1" class="button-option">Comenzar <span class="ml-2">🚀</span></button>
                </div>
            </div>

            <hr class="my-6 border-t-2 border-gray-600 rounded-full shadow-lg"> <!-- Línea divisoria -->

            <!-- Sección de noticias/log -->
            <div class="news-feed-section section-card">
                <h2 class="text-2xl font-bold mb-4 text-center text-gray-200">Registro de Noticias <span class="ml-2">📰</span></h2>
                <div id="newsFeed" class="flex flex-col-reverse gap-3">
                    <!-- Las noticias se añadirán aquí -->
                </div>
            </div>
        </div>

        <!-- Footer del juego con botones de economía y estadísticas -->
        <div id="gameFooter" class="game-footer hidden">
            <button id="btnOpenEconomicModal" class="footer-button">
                <span class="mr-2">💰</span> Economía
            </button>
            <button id="btnOpenGeneralStatsModal" class="footer-button">
                <span class="mr-2">📊</span> Estadísticas
            </button>
            <button id="btnOpenRelationsModal" class="footer-button">
                <span class="mr-2">🌍</span> Diplomacia
            </button>
            <button id="btnOpenFundAllocationModal" class="footer-button">
                <span class="mr-2">💸</span> Destinar Fondos
            </button>
            <button id="btnOpenIdeologyModal" class="footer-button">
                <span class="mr-2">💡</span> Ideología
            </button>
            <button id="btnOpenMilitaryModal" class="footer-button">
                <span class="mr-2">⚔️</span> Ejército
            </button>
            <button id="btnOpenSettingsModal" class="footer-button">
                <span class="mr-2">⚙️</span> Configuración
            </button>
        </div>

        <!-- Nuevo Modal de Configuración (oculto por defecto) -->
        <div id="settingsModal" class="modal-overlay">
            <div class="modal-content section-card">
                <button id="btnCloseSettingsModal" class="modal-close-button">❌</button>
                <h2 class="text-2xl font-bold mb-4 text-center text-gray-200">Configuración <span class="ml-2">⚙️</span></h2>

                <div class="audio-controls">
                    <button id="playPauseMusicBtn">🎵</button>
                    <input type="range" id="musicVolumeSlider" min="0" max="1" step="0.01" value="0.5">
                </div>
            </div>
        </div>

        <!-- Modal de Detalles Económicos (oculto por defecto) -->
        <div id="economicModal" class="modal-overlay">
            <div class="modal-content section-card">
                <button id="btnCloseEconomicModal" class="modal-close-button">❌</button>
                <h2 class="text-2xl font-bold mb-4 text-center text-gray-200">Detalles Económicos <span class="ml-2">💰</span></h2>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <p class="text-lg font-semibold mb-2 text-indigo-300 flex justify-between items-center">
                            <span><span class="mr-1">📈</span> PIB:</span> <span id="gdpValueModal" class="text-xl font-bold">0</span> B USD
                        </p>
                    </div>
                    <div>
                        <p class="text-lg font-semibold mb-2 text-yellow-200 flex justify-between items-center">
                            <span><span class="mr-1">🏦</span> Tesorería:</span> <span id="treasuryValueModal" class="text-xl font-bold">0</span> B USD
                        </p>
                    </div>
                    <div>
                        <p class="text-lg font-semibold mb-2 text-green-200 flex justify-between items-center">
                            <span><span class="mr-1">💵</span> Generado Total:</span> <span id="totalMoneyGeneratedValueModal" class="text-xl font-bold">0</span> B USD
                        </p>
                    </div>
                    <div>
                        <p class="text-lg font-semibold mb-2 text-emerald-300 flex justify-between items-center">
                            <span><span class="mr-1">🧾</span> Impuestos:</span> <span id="taxRevenueValueModal" class="text-xl font-bold">0</span> B USD
                        </p>
                    </div>
                    <div>
                        <p class="text-lg font-semibold mb-2 text-lime-300 flex justify-between items-center">
                            <span><span class="mr-1">🏭</span> Industria:</span> <span id="industryRevenueValueModal" class="text-xl font-bold">0</span> B USD
                        </p>
                    </div>
                    <div>
                        <p class="text-lg font-semibold mb-2 text-orange-300 flex justify-between items-center">
                            <span><span class="mr-1">💸</span> Gastos:</span> <span id="maintenanceCostsValueModal" class="text-xl font-bold">0</span> B USD
                        </p>
                    </div>
                    <div>
                        <p class="text-lg font-semibold mb-2 text-red-400 flex justify-between items-center">
                            <span><span class="mr-1">🔥</span> Inflación:</span> <span id="inflationRateValueModal" class="text-xl font-bold">0</span>%
                        </p>
                    </div>
                    <div>
                        <p class="text-lg font-semibold mb-2 text-red-600 flex justify-between items-center">
                            <span><span class="mr-1">债</span> Deuda:</span> <span id="debtValueModal" class="text-xl font-bold">0</span> B USD
                        </p>
                    </div>
                    <div>
                        <p class="text-lg font-semibold mb-2 text-blue-300 flex justify-between items-center">
                            <span><span class="mr-1">💲</span> Valor Moneda:</span> <span id="currencyValueModal" class="text-xl font-bold">100</span>%
                        </p>
                    </div>
                    <div class="md:col-span-2">
                        <p class="text-2xl font-bold mt-4 text-center text-cyan-300 flex justify-between items-center">
                            <span><span class="mr-1">⚖️</span> Balance Anual:</span> <span id="annualBalanceValueModal" class="text-2xl font-bold">0</span> B USD
                        </p>
                    </div>
                </div>

                <h3 class="text-xl font-bold mt-6 mb-4 text-center text-gray-200">Acciones Financieras</h3>
                <div class="grid grid-cols-1 gap-3">
                    <div class="economic-action-group">
                        <button id="btnPayDebt" class="button-option bg-green-700 hover:bg-green-800">
                            <span class="mr-2">💳</span> Pagar Deuda
                        </button>
                        <input type="number" id="payDebtAmount" placeholder="Monto (B USD)" min="1" class="rounded-lg">
                    </div>
                    <div class="economic-action-group">
                        <button id="btnPrintMoney" class="button-option bg-yellow-700 hover:bg-yellow-800">
                            <span class="mr-2">🖨️</span> Imprimir Dinero
                        </button>
                        <input type="number" id="printMoneyAmount" placeholder="Monto (B USD)" min="1" class="rounded-lg">
                    </div>
                    <div class="economic-action-group">
                        <button id="btnTakeLoan" class="button-option bg-blue-700 hover:bg-blue-800">
                            <span class="mr-2">🤝</span> Sacar Préstamo
                        </button>
                        <input type="number" id="takeLoanAmount" placeholder="Monto (B USD)" min="1" class="rounded-lg">
                    </div>
                </div>
            </div>
        </div>

        <!-- Nuevo Modal de Estadísticas Generales (oculto por defecto) -->
        <div id="generalStatsModal" class="modal-overlay">
            <div class="modal-content section-card">
                <button id="btnCloseGeneralStatsModal" class="modal-close-button">❌</button>
                <h2 class="text-2xl font-bold mb-4 text-center text-gray-200">Estadísticas Generales <span class="ml-2">📊</span></h2>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <!-- Economía -->
                    <div>
                        <p class="text-lg font-semibold mb-2 text-yellow-300 flex justify-between items-center">
                            <span>Economía:</span> <span id="economyValueModal" class="text-xl font-bold">100</span>%
                        </p>
                        <div class="stat-bar-container">
                            <div id="economyBarModal" class="stat-bar" style="width: 100%;"></div>
                        </div>
                    </div>
                    <!-- Felicidad -->
                    <div>
                        <p class="text-lg font-semibold mb-2 text-pink-300 flex justify-between items-center">
                            <span>Felicidad:</span> <span id="happinessValueModal" class="text-xl font-bold">100</span>%
                        </p>
                        <div class="stat-bar-container">
                            <div id="happinessBarModal" class="stat-bar" style="width: 100%;"></div>
                        </div>
                    </div>
                    <!-- Estabilidad -->
                    <div>
                        <p class="text-lg font-semibold mb-2 text-red-300 flex justify-between items-center">
                            <span>Estabilidad:</span> <span id="stabilityValueModal" class="text-xl font-bold">100</span>%
                        </p>
                        <div class="stat-bar-container">
                            <div id="stabilityBarModal" class="stat-bar" style="width: 100%;"></div>
                        </div>
                    </div>
                    <!-- Relaciones Internacionales -->
                    <div>
                        <p class="text-lg font-semibold mb-2 text-purple-300 flex justify-between items-center">
                            <span>Relaciones Internacionales:</span> <span id="relationsValueModal" class="text-xl font-bold">100</span>%
                        </p>
                        <div class="stat-bar-container">
                            <div id="relationsBarModal" class="stat-bar" style="width: 100%;"></div>
                        </div>
                    </div>
                    <!-- Población -->
                    <div>
                        <p class="text-lg font-semibold mb-2 text-green-300 flex justify-between items-center">
                            <span>Población:</span> <span id="populationValueModal" class="text-xl font-bold">0</span> M
                        </p>
                    </div>
                    <!-- Desarrollo Tecnológico -->
                    <div>
                        <p class="text-lg font-semibold mb-2 text-teal-300 flex justify-between items-center">
                            <span>Desarrollo Tecnológico:</span> <span id="techDevelopmentValueModal" class="text-xl font-bold">0</span>%
                        </p>
                    </div>
                    <!-- Puntuación Diplomática -->
                    <div>
                        <p class="text-lg font-semibold mb-2 text-blue-200 flex justify-between items-center">
                            <span>Puntuación Diplomática:</span> <span id="diplomaticScoreValueModal" class="text-xl font-bold">0</span>%
                        </p>
                    </div>
                    <!-- Eficiencia Gubernamental -->
                    <div>
                        <p class="text-lg font-semibold mb-2 text-gray-400 flex justify-between items-center">
                            <span>Eficiencia Gubernamental:</span> <span id="efficiencyValueModal" class="text-xl font-bold">100</span>%
                        </p>
                        <div class="stat-bar-container">
                            <div id="efficiencyBarModal" class="stat-bar" style="width: 100%;"></div>
                        </div>
                    </div>
                    <!-- Ideología -->
                    <div>
                        <p class="text-lg font-semibold mb-2 text-orange-300 flex justify-between items-center">
                            <span>Ideología:</span> <span id="ideologyValueModal" class="text-xl font-bold">Capitalismo</span>
                        </p>
                    </div>
                    <!-- Ejército -->
                    <div>
                        <p class="text-lg font-semibold mb-2 text-red-500 flex justify-between items-center">
                            <span>Ejército:</span> <span id="armySizeValueModal" class="text-xl font-bold">0</span>k
                        </p>
                    </div>
                </div>
            </div>
        </div>

        <!-- Nuevo Modal de Relaciones Diplomáticas (oculto por defecto) -->
        <div id="relationsModal" class="modal-overlay">
            <div class="modal-content section-card">
                <button id="btnCloseRelationsModal" class="modal-close-button">❌</button>
                <h2 class="text-2xl font-bold mb-4 text-center text-gray-200">Acciones Diplomáticas <span class="ml-2">🌍</span></h2>
                <p class="text-lg text-gray-300 mb-4 text-center">Elige una acción diplomática con otra nación:</p>
                <div class="w-full max-w-xs mx-auto mb-6">
                    <select id="diplomacyCountrySelect" class="country-select">
                        <!-- Options will be populated by JS -->
                    </select>
                    <p class="text-lg text-gray-300 mt-2 text-center">Relación con <span id="selectedDiplomacyCountryName">Nación</span>: <span id="bilateralRelationsValue">--</span>%</p>
                </div>

                <!-- New section for selected country's stats -->
                <div id="selectedCountryStats" class="section-card mt-4 p-4 text-center hidden">
                    <h3 class="text-xl font-bold mb-3 text-gray-200">Estadísticas de <span id="selectedCountryNameStats">Nación</span> <span class="ml-2">📊</span></h3>
                    <div class="grid grid-cols-2 gap-3 text-lg">
                        <p class="text-yellow-300"><span class="mr-1">📈</span> Economía: <span id="otherCountryEconomy">--</span>%</p>
                        <p class="text-pink-300"><span class="mr-1">😊</span> Felicidad: <span id="otherCountryHappiness">--</span>%</p>
                        <p class="text-red-300"><span class="mr-1">🛡️</span> Estabilidad: <span id="otherCountryStability">--</span>%</p>
                        <p class="text-teal-300"><span class="mr-1">🔬</span> Tecnología: <span id="otherCountryTech">--</span>%</p>
                        <p class="text-red-500 col-span-2"><span class="mr-1">💂</span> Ejército: <span id="otherCountryArmySize">--</span>k</p>
                    </div>
                </div>

                <div class="grid grid-cols-1 gap-4 mt-6">
                    <button id="btnBreakRelations" class="button-option bg-red-700 hover:bg-red-800">
                        <span class="mr-2">💔</span> Romper Relaciones
                    </button>
                    <button id="btnIncreaseRelations" class="button-option bg-green-700 hover:bg-green-800">
                        <span class="mr-2">✨</span> Mejorar Relaciones
                    </button>
                    <button id="btnTradeAgreement" class="button-option bg-blue-700 hover:bg-blue-800">
                        <span class="mr-2">🤝</span> Acuerdo Comercial
                    </button>
                    <button id="btnDeclareWar" class="button-option bg-red-900 hover:bg-red-950">
                        <span class="mr-2">⚔️</span> Declarar Guerra
                    </button>
                    <button id="btnSendGift" class="button-option bg-yellow-700 hover:bg-yellow-800">
                        <span class="mr-2">🎁</span> Enviar Regalo
                    </button>
                    <button id="btnFormAlliance" class="button-option bg-indigo-700 hover:bg-indigo-800">
                        <span class="mr-2">🔗</span> Formar Alianza
                    </button>
                </div>
            </div>
        </div>

        <!-- Nuevo Modal de Asignación de Fondos (oculto por defecto) -->
        <div id="fundAllocationModal" class="modal-overlay">
            <div class="modal-content section-card">
                <button id="btnCloseFundAllocationModal" class="modal-close-button">❌</button>
                <h2 class="text-2xl font-bold mb-4 text-center text-gray-200">Asignación de Fondos <span class="ml-2">💸</span></h2>
                <p class="text-lg text-gray-300 mb-4 text-center">
                    Balance Anual Disponible: <span id="currentAnnualBalanceDisplay" class="font-bold text-green-300">0</span> B USD
                </p>
                <div id="fundAllocationInputs" class="grid grid-cols-1 gap-3">
                    <!-- Inputs de asignación de fondos se generarán aquí -->
                </div>
                <p id="totalAllocatedPercentageDisplay" class="total-allocated-display">
                    Total Asignado: <span id="totalAllocatedValue">0</span>%
                </p>
                <button id="btnConfirmFundAllocation" class="button-option mt-6 w-full">
                    <span class="mr-2">✅</span> Confirmar Asignación
                </button>
            </div>
        </div>

        <!-- Nuevo Modal de Ideologías (oculto por defecto) -->
        <div id="ideologyModal" class="modal-overlay">
            <div class="modal-content section-card">
                <button id="btnCloseIdeologyModal" class="modal-close-button">❌</button>
                <h2 class="text-2xl font-bold mb-4 text-center text-gray-200">Cambiar Ideología <span class="ml-2">💡</span></h2>
                <p class="text-lg text-gray-300 mb-4 text-center">
                    Costo por cambio de ideología: <span class="font-bold text-yellow-300">10.00 B USD</span>
                </p>
                <div id="ideologyCardsContainer" class="grid grid-cols-1 gap-4">
                    <!-- Ideology cards will be populated here -->
                </div>
                <button id="btnApplyIdeology" class="button-option mt-6 w-full hidden">
                    <span class="mr-2">✅</span> Aplicar Ideología
                </button>
            </div>
        </div>

        <!-- Nuevo Modal de Ejército (oculto por defecto) -->
        <div id="militaryModal" class="modal-overlay">
            <div class="modal-content section-card">
                <button id="btnCloseMilitaryModal" class="modal-close-button">❌</button>
                <h2 class="text-2xl font-bold mb-4 text-center text-gray-200">Gestión del Ejército <span class="ml-2">⚔️</span></h2>
                <p class="military-stat-display">
                    Tamaño actual del Ejército: <span id="currentArmySizeDisplay">0</span>k tropas
                </p>

                <div class="military-action-group">
                    <h3 class="text-xl font-bold text-gray-200 mb-2">Entrenar Ejército 🏋️‍♂️</h3>
                    <p class="text-sm text-gray-400 mb-2">Costo: 0.5 B USD por 1k tropas. Aumenta el tamaño y la eficiencia del ejército.</p>
                    <label for="trainArmyAmount">Cantidad de tropas a entrenar (miles):</label>
                    <input type="number" id="trainArmyAmount" placeholder="Ej: 10" min="1" value="10">
                    <button id="btnTrainArmy" class="button-option bg-blue-700 hover:bg-blue-800">
                        <span class="mr-2">💪</span> Entrenar
                    </button>
                </div>

                <div class="military-action-group">
                    <h3 class="text-xl font-bold text-gray-200 mb-2">Contratar Mercenarios 🤑</h3>
                    <p class="text-sm text-gray-400 mb-2">Costo: 1.5 B USD por 1k tropas. Rápido, pero puede afectar la felicidad y relaciones.</p>
                    <label for="hireMercenariesAmount">Cantidad de mercenarios a contratar (miles):</label>
                    <input type="number" id="hireMercenariesAmount" placeholder="Ej: 5" min="1" value="5">
                    <button id="btnHireMercenaries" class="button-option bg-yellow-700 hover:bg-yellow-800">
                        <span class="mr-2">🤝</span> Contratar
                    </button>
                </div>

                <div class="military-action-group">
                    <h3 class="text-xl font-bold text-gray-200 mb-2">Reclutar Voluntarios 📣</h3>
                    <p class="text-sm text-gray-400 mb-2">Costo: Gratis. Tropas menos entrenadas, puede afectar la estabilidad.</p>
                    <label for="recruitVolunteersAmount">Cantidad de voluntarios a reclutar (miles):</label>
                    <input type="number" id="recruitVolunteersAmount" placeholder="Ej: 20" min="1" value="20">
                    <button id="btnRecruitVolunteers" class="button-option bg-green-700 hover:bg-green-800">
                        <span class="mr-2">🕊️</span> Reclutar
                    </button>
                </div>
            </div>
        </div>


        <!-- Mensaje de juego terminado/alerta (oculto por defecto) -->
        <div id="messageBox" class="message-box">
            <p id="messageText" class="mb-4"></p>
            <button id="closeMessage" class="button-option mt-4 mx-auto">
                <span class="mr-2">❌</span> Cerrar
            </button>
        </div>

        <!-- Nuevo Modal de Confirmación (oculto por defecto) -->
        <div id="confirmationModal" class="modal-overlay">
            <div class="confirmation-modal-content section-card">
                <h3 id="confirmationTitle"></h3>
                <p id="confirmationMessage"></p>
                <div class="button-group">
                    <button id="btnConfirmAction" class="button-option bg-green-600 hover:bg-green-700">
                        <span class="mr-2">✅</span> Confirmar
                    </button>
                    <button id="btnCancelAction" class="button-option bg-red-600 hover:bg-red-700">
                        <span class="mr-2">❌</span> Cancelar
                    </button>
                </div>
            </div>
        </div>

    </div>

    <!-- Audio para música de fondo -->
    <audio id="backgroundMusic" loop>
        <source src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3" type="audio/mpeg">
        Tu navegador no soporta el elemento de audio.
    </audio>

    <script>
        // Game variables
        let economy = 100;
        let happiness = 100;
        let stability = 100;
        let relations = 100; // Overall international relations
        let playerRelations = new Map(); // Specific relations with other countries
        let population = 0; // Population in millions
        let gdp = 0; // GDP in billions USD
        let techDevelopment = 0; // Technological Development in %
        let treasury = 0; // Money saved (in billions USD)
        let totalMoneyGenerated = 0; // Cumulative money generated (in billions USD)
        let diplomaticScore = 0; // Diplomatic agreements score (0-100%)
        let inflationRate = 2.0; // Initial inflation rate in percentage
        let debt = 0; // New: Debt in billions USD
        let currencyValue = 1.0; // New: 1.0 represents 100% of its initial value
        let unAidCount = 0; // New: Counter for UN aid received (max 3)
        let efficiency = 100; // New: Government efficiency (0-100%)
        let currentIdeology = "Capitalismo"; // New: Default ideology
        let armySize = 0; // New: Army size in thousands

        // Economic breakdown variables
        let taxRevenue = 0; // Annual tax revenue
        let industryRevenue = 0; // Annual industry revenue
        let maintenanceCosts = 0; // Annual maintenance costs
        let annualBalance = 0; // Annual balance (income - expenses)

        let year = 1;
        let currentMonth = 1; // New: Track current month for display
        let currentCountry = "Nación Unida"; // Default country
        let currentCountryFlagEmoji = ""; // Emoji flag of the current country
        let countryChallenges = []; // New: Array to store active country-specific challenges

        let gameEventsQueue = []; // New: Queue for events in the current turn

        // DOM element references
        const mainMenuScreen = document.getElementById('mainMenuScreen');
        const countrySelectionScreen = document.getElementById('countrySelectionScreen');
        const gameScreen = document.getElementById('gameScreen');
        const gameFooter = document.getElementById('gameFooter');

        const btnChooseCountry = document.getElementById('btnChooseCountry');
        const btnContinueGame = document.getElementById('btnContinueGame');
        const countrySearchInput = document.getElementById('countrySearchInput');
        const countrySelect = document.getElementById('countrySelect');
        const btnConfirmCountry = document.getElementById('btnConfirmCountry');
        const btnBackToMain = document.getElementById('btnBackToMain');

        // General stats on main screen (now simplified)
        const economyValue = document.getElementById('economyValue');
        const happinessValue = document.getElementById('happinessValue');
        const stabilityValue = document.getElementById('stabilityValue');
        const relationsValue = document.getElementById('relationsValue');
        const populationValue = document.getElementById('populationValue');
        const techDevelopmentValue = document.getElementById('techDevelopmentValue');
        const diplomaticScoreValue = document.getElementById('diplomaticScoreValue');
        const efficiencyValue = document.getElementById('efficiencyValue'); // New efficiency display
        const ideologyValue = document.getElementById('ideologyValue'); // New ideology display
        const armySizeValue = document.getElementById('armySizeValue'); // New army size display

        // Economic breakdown DOM elements (for modal)
        const gdpValueModal = document.getElementById('gdpValueModal');
        const treasuryValueModal = document.getElementById('treasuryValueModal');
        const totalMoneyGeneratedValueModal = document.getElementById('totalMoneyGeneratedValueModal');
        const taxRevenueValueModal = document.getElementById('taxRevenueValueModal');
        const industryRevenueValueModal = document.getElementById('industryRevenueValueModal');
        const maintenanceCostsValueModal = document.getElementById('maintenanceCostsValueModal');
        const annualBalanceValueModal = document.getElementById('annualBalanceValueModal');
        const inflationRateValueModal = document.getElementById('inflationRateValueModal'); // New inflation display
        const debtValueModal = document.getElementById('debtValueModal'); // New debt display
        const currencyValueModal = document.getElementById('currencyValueModal'); // New currency value display

        // General stats DOM elements (for new modal with bars)
        const economyValueModal = document.getElementById('economyValueModal');
        const happinessValueModal = document.getElementById('happinessValueModal');
        const stabilityValueModal = document.getElementById('stabilityValueModal');
        const relationsValueModal = document.getElementById('relationsValueModal');
        const populationValueModal = document.getElementById('populationValueModal');
        const techDevelopmentValueModal = document.getElementById('techDevelopmentValueModal');
        const diplomaticScoreValueModal = document.getElementById('diplomaticScoreValueModal');
        const efficiencyValueModal = document.getElementById('efficiencyValueModal'); // New efficiency modal display
        const ideologyValueModal = document.getElementById('ideologyValueModal'); // New ideology modal display
        const armySizeValueModal = document.getElementById('armySizeValueModal'); // New army size modal display

        const economyBarModal = document.getElementById('economyBarModal');
        const happinessBarModal = document.getElementById('happinessBarModal');
        const stabilityBarModal = document.getElementById('stabilityBarModal');
        const relationsBarModal = document.getElementById('relationsBarModal');
        const efficiencyBarModal = document.getElementById('efficiencyBarModal'); // New efficiency bar

        const yearDisplay = document.getElementById('year');
        const monthDisplay = document.getElementById('month'); // New month display
        const currentCountryDisplay = document.getElementById('currentCountryDisplay');
        const countryFlagEmoji = document.getElementById('countryFlagEmoji');

        const eventText = document.getElementById('eventText');
        const optionsContainer = document.getElementById('optionsContainer');
        const messageBox = document.getElementById('messageBox');
        const messageText = document.getElementById('messageText');
        const closeMessageButton = document.getElementById('closeMessage');

        // News Feed elements
        const newsFeedContainer = document.getElementById('newsFeed');

        // Country Challenges elements
        const countryChallengesSection = document.getElementById('countryChallengesSection');
        const activeChallengesList = document.getElementById('activeChallengesList');

        // Modals
        const economicModal = document.getElementById('economicModal');
        const generalStatsModal = document.getElementById('generalStatsModal');
        const relationsModal = document.getElementById('relationsModal');
        const fundAllocationModal = document.getElementById('fundAllocationModal');
        const settingsModal = document.getElementById('settingsModal'); // New settings modal
        const ideologyModal = document.getElementById('ideologyModal'); // New ideology modal
        const militaryModal = document.getElementById('militaryModal'); // New military modal

        // Buttons for modals
        const btnOpenEconomicModal = document.getElementById('btnOpenEconomicModal'); // Moved back to footer
        const btnCloseEconomicModal = document.getElementById('btnCloseEconomicModal');
        const btnOpenGeneralStatsModal = document.getElementById('btnOpenGeneralStatsModal'); // Moved back to footer
        const btnCloseGeneralStatsModal = document.getElementById('btnCloseGeneralStatsModal');
        const btnOpenRelationsModal = document.getElementById('btnOpenRelationsModal'); // Moved back to footer
        const btnCloseRelationsModal = document.getElementById('btnCloseRelationsModal');
        const btnOpenFundAllocationModal = document.getElementById('btnOpenFundAllocationModal'); // Moved back to footer
        const btnCloseFundAllocationModal = document.getElementById('btnCloseFundAllocationModal');
        const btnOpenSettingsModal = document.getElementById('btnOpenSettingsModal'); // New button to open settings
        const btnCloseSettingsModal = document.getElementById('btnCloseSettingsModal'); // New button to close settings
        const btnOpenIdeologyModal = document.getElementById('btnOpenIdeologyModal'); // New button for ideology modal
        const btnCloseIdeologyModal = document.getElementById('btnCloseIdeologyModal'); // New button for ideology modal
        const btnOpenMilitaryModal = document.getElementById('btnOpenMilitaryModal'); // New button for military modal
        const btnCloseMilitaryModal = document.getElementById('btnCloseMilitaryModal'); // New button for military modal

        // Relations Modal elements
        const diplomacyCountrySelect = document.getElementById('diplomacyCountrySelect');
        const selectedDiplomacyCountryName = document.getElementById('selectedDiplomacyCountryName');
        const bilateralRelationsValue = document.getElementById('bilateralRelationsValue');
        const selectedCountryStatsSection = document.getElementById('selectedCountryStats'); // New section
        const selectedCountryNameStats = document.getElementById('selectedCountryNameStats'); // New element
        const otherCountryEconomy = document.getElementById('otherCountryEconomy'); // New element
        const otherCountryHappiness = document.getElementById('otherCountryHappiness'); // New element
        const otherCountryStability = document.getElementById('otherCountryStability'); // New element
        const otherCountryTech = document.getElementById('otherCountryTech'); // New element
        const otherCountryArmySize = document.getElementById('otherCountryArmySize'); // New element

        const btnBreakRelations = document.getElementById('btnBreakRelations');
        const btnIncreaseRelations = document.getElementById('btnIncreaseRelations');
        const btnTradeAgreement = document.getElementById('btnTradeAgreement');
        // const btnScientificAgreement = document.getElementById('btnScientificAgreement'); // Removed
        const btnDeclareWar = document.getElementById('btnDeclareWar');
        const btnSendGift = document.getElementById('btnSendGift');
        const btnFormAlliance = document.getElementById('btnFormAlliance');
        // const btnCulturalExchange = document.getElementById('btnCulturalExchange'); // Removed
        // const btnEspionage = document.getElementById('btnEspionage'); // Removed
        // const btnHumanitarianAid = document.getElementById('btnHumanitarianAid'); // Removed

        // Fund Allocation Modal elements
        const currentAnnualBalanceDisplay = document.getElementById('currentAnnualBalanceDisplay');
        const fundAllocationInputsContainer = document.getElementById('fundAllocationInputs');
        const totalAllocatedPercentageDisplay = document.getElementById('totalAllocatedPercentageDisplay');
        const totalAllocatedValue = document.getElementById('totalAllocatedValue');
        const btnConfirmFundAllocation = document.getElementById('btnConfirmFundAllocation');

        // Economic Action Buttons and Inputs
        const btnPayDebt = document.getElementById('btnPayDebt');
        const payDebtAmountInput = document.getElementById('payDebtAmount');
        const btnPrintMoney = document.getElementById('btnPrintMoney');
        const printMoneyAmountInput = document.getElementById('printMoneyAmount');
        const btnTakeLoan = document.getElementById('btnTakeLoan');
        const takeLoanAmountInput = document.getElementById('takeLoanAmount');

        // Ideology Modal elements
        const ideologyCardsContainer = document.getElementById('ideologyCardsContainer');
        const btnApplyIdeology = document.getElementById('btnApplyIdeology');
        let selectedIdeologyKey = null; // To store the key of the currently selected ideology

        // Military Modal elements
        const currentArmySizeDisplay = document.getElementById('currentArmySizeDisplay');
        const trainArmyAmountInput = document.getElementById('trainArmyAmount');
        const btnTrainArmy = document.getElementById('btnTrainArmy');
        const hireMercenariesAmountInput = document.getElementById('hireMercenariesAmount');
        const btnHireMercenaries = document.getElementById('btnHireMercenaries');
        const recruitVolunteersAmountInput = document.getElementById('recruitVolunteersAmount');
        const btnRecruitVolunteers = document.getElementById('btnRecruitVolunteers');

        // Confirmation Modal Elements
        const confirmationModal = document.getElementById('confirmationModal');
        const confirmationTitle = document.getElementById('confirmationTitle');
        const confirmationMessage = document.getElementById('confirmationMessage');
        const btnConfirmAction = document.getElementById('btnConfirmAction');
        const btnCancelAction = document.getElementById('btnCancelAction');

        // Callback for confirmation
        let onConfirmCallback = null;

        // Audio elements
        const backgroundMusic = document.getElementById('backgroundMusic');
        const playPauseMusicBtn = document.getElementById('playPauseMusicBtn');
        const musicVolumeSlider = document.getElementById('musicVolumeSlider');
        const clickSound = new Audio('https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3'); // Placeholder, replace with a short click sound if available and appropriate.
        clickSound.volume = 0.5; // Set a default volume for click sound


        // Data for 100 most important/influential countries (based on population and global relevance)
        const countryData = {
            "Afganistán": { flag: "🇦🇫", initialStats: { economy: 40, happiness: 30, stability: 20, relations: 30, population: 40, gdp: 20, techDevelopment: 10, treasury: 5, diplomaticScore: 30, debt: 0, efficiency: 40, ideology: "Teocracia", armySize: 100 } },
            "Alemania": { flag: "�🇪", initialStats: { economy: 85, happiness: 80, stability: 90, relations: 85, population: 83, gdp: 4000, techDevelopment: 90, treasury: 400, diplomaticScore: 85, debt: 0, efficiency: 90, ideology: "Democracia", armySize: 180 } },
            "Arabia Saudita": { flag: "🇸🇦", initialStats: { economy: 75, happiness: 65, stability: 80, relations: 70, population: 35, gdp: 800, techDevelopment: 60, treasury: 100, diplomaticScore: 70, debt: 0, efficiency: 70, ideology: "Monarquía", armySize: 225 } },
            "Argentina": { flag: "🇦🇷", initialStats: { economy: 60, happiness: 70, stability: 50, relations: 60, population: 45, gdp: 450, techDevelopment: 55, treasury: 50, diplomaticScore: 60, debt: 50, efficiency: 55, ideology: "Democracia", armySize: 80 } },
            "Australia": { flag: "🇦🇺", initialStats: { economy: 80, happiness: 85, stability: 85, relations: 80, population: 26, gdp: 1500, techDevelopment: 80, treasury: 150, diplomaticScore: 80, debt: 0, efficiency: 85, ideology: "Democracia", armySize: 60 } },
            "Austria": { flag: "🇦🇹", initialStats: { economy: 80, happiness: 80, stability: 85, relations: 80, population: 9, gdp: 500, techDevelopment: 85, treasury: 60, diplomaticScore: 80, debt: 0, efficiency: 85, ideology: "Democracia", armySize: 25 } },
            "Bangladés": { flag: "🇧🇩", initialStats: { economy: 55, happiness: 50, stability: 45, relations: 50, population: 170, gdp: 400, techDevelopment: 30, treasury: 30, diplomaticScore: 50, debt: 20, efficiency: 45, ideology: "Democracia", armySize: 160 } },
            "Bélgica": { flag: "🇧🇪", initialStats: { economy: 80, happiness: 80, stability: 85, relations: 85, population: 11, gdp: 600, techDevelopment: 85, treasury: 70, diplomaticScore: 85, debt: 0, efficiency: 85, ideology: "Democracia", armySize: 25 } },
            "Bielorrusia": { flag: "🇧🇾", initialStats: { economy: 50, happiness: 45, stability: 60, relations: 40, population: 9, gdp: 70, techDevelopment: 40, treasury: 10, diplomaticScore: 40, debt: 10, efficiency: 50, ideology: "Autoritarismo", armySize: 45 } },
            "Bolivia": { flag: "🇧🇴", initialStats: { economy: 45, happiness: 55, stability: 40, relations: 45, population: 12, gdp: 40, techDevelopment: 25, treasury: 5, diplomaticScore: 45, debt: 5, efficiency: 40, ideology: "Socialismo", armySize: 50 } },
            "Brasil": { flag: "🇧🇷", initialStats: { economy: 70, happiness: 65, stability: 60, relations: 70, population: 215, gdp: 1800, techDevelopment: 65, treasury: 180, diplomaticScore: 70, debt: 100, efficiency: 60, ideology: "Democracia", armySize: 360 } },
            "Bulgaria": { flag: "🇧🇬", initialStats: { economy: 60, happiness: 65, stability: 70, relations: 65, population: 7, gdp: 90, techDevelopment: 50, treasury: 10, diplomaticScore: 65, debt: 10, efficiency: 65, ideology: "Democracia", armySize: 30 } },
            "Burkina Faso": { flag: "🇧🇫", initialStats: { economy: 30, happiness: 25, stability: 20, relations: 25, population: 22, gdp: 20, techDevelopment: 5, treasury: 2, diplomaticScore: 25, debt: 5, efficiency: 25, ideology: "Militarismo", armySize: 15 } },
            "Camboya": { flag: "🇰🇭", initialStats: { economy: 45, happiness: 50, stability: 40, relations: 45, population: 17, gdp: 30, techDevelopment: 20, treasury: 3, diplomaticScore: 45, debt: 5, efficiency: 35, ideology: "Monarquía", armySize: 120 } },
            "Camerún": { flag: "🇨🇲", initialStats: { economy: 40, happiness: 35, stability: 30, relations: 35, population: 28, gdp: 40, techDevelopment: 15, treasury: 4, diplomaticScore: 35, debt: 5, efficiency: 30, ideology: "Autoritarismo", armySize: 25 } },
            "Canadá": { flag: "🇨🇦", initialStats: { economy: 88, happiness: 90, stability: 92, relations: 90, population: 38, gdp: 2000, techDevelopment: 92, treasury: 200, diplomaticScore: 90, debt: 0, efficiency: 92, ideology: "Democracia", armySize: 65 } },
            "Chad": { flag: "🇹🇩", initialStats: { economy: 25, happiness: 20, stability: 15, relations: 20, population: 17, gdp: 10, techDevelopment: 5, treasury: 1, diplomaticScore: 20, debt: 5, efficiency: 20, ideology: "Militarismo", armySize: 30 } },
            "Chile": { flag: "🇨🇱", initialStats: { economy: 70, happiness: 75, stability: 65, relations: 70, population: 19, gdp: 300, techDevelopment: 70, treasury: 30, diplomaticScore: 70, debt: 10, efficiency: 70, ideology: "Democracia", armySize: 80 } },
            "China": { flag: "🇨🇳", initialStats: { economy: 90, happiness: 60, stability: 95, relations: 70, population: 1400, gdp: 18000, techDevelopment: 90, treasury: 1800, diplomaticScore: 70, debt: 0, efficiency: 80, ideology: "Comunismo", armySize: 2000 } },
            "Colombia": { flag: "🇨🇴", initialStats: { economy: 65, happiness: 60, stability: 55, relations: 60, population: 51, gdp: 350, techDevelopment: 50, treasury: 35, diplomaticScore: 60, debt: 20, efficiency: 55, ideology: "Democracia", armySize: 250 } },
            "Corea del Norte": { flag: "🇰🇵", initialStats: { economy: 10, happiness: 5, stability: 99, relations: 5, population: 26, gdp: 30, techDevelopment: 10, treasury: 1, diplomaticScore: 5, debt: 5, efficiency: 30, ideology: "Totalitarismo", armySize: 1200 } },
            "Corea del Sur": { flag: "🇰🇷", initialStats: { economy: 88, happiness: 75, stability: 85, relations: 80, population: 51, gdp: 1700, techDevelopment: 95, treasury: 170, diplomaticScore: 80, debt: 0, efficiency: 90, ideology: "Democracia", armySize: 500 } },
            "Costa de Marfil": { flag: "🇨🇮", initialStats: { economy: 50, happiness: 45, stability: 40, relations: 45, population: 27, gdp: 70, techDevelopment: 25, treasury: 7, diplomaticScore: 45, debt: 10, efficiency: 40, ideology: "Democracia", armySize: 25 } },
            "Cuba": { flag: "🇨🇺", initialStats: { economy: 30, happiness: 60, stability: 70, relations: 30, population: 11, gdp: 100, techDevelopment: 35, treasury: 10, diplomaticScore: 30, debt: 20, efficiency: 40, ideology: "Comunismo", armySize: 50 } },
            "Ecuador": { flag: "🇪🇨", initialStats: { economy: 55, happiness: 60, stability: 50, relations: 55, population: 18, gdp: 110, techDevelopment: 45, treasury: 11, diplomaticScore: 55, debt: 15, efficiency: 50, ideology: "Democracia", armySize: 40 } },
            "Egipto": { flag: "🇪🇬", initialStats: { economy: 60, happiness: 55, stability: 65, relations: 60, population: 105, gdp: 400, techDevelopment: 40, treasury: 40, diplomaticScore: 60, debt: 30, efficiency: 55, ideology: "Autoritarismo", armySize: 450 } },
            "El Salvador": { flag: "🇸🇻", initialStats: { economy: 45, happiness: 50, stability: 40, relations: 45, population: 6, gdp: 30, techDevelopment: 30, treasury: 3, diplomaticScore: 45, debt: 5, efficiency: 40, ideology: "Democracia", armySize: 25 } },
            "Emiratos Árabes Unidos": { flag: "🇦🇪", initialStats: { economy: 95, happiness: 85, stability: 90, relations: 88, population: 10, gdp: 400, techDevelopment: 90, treasury: 50, diplomaticScore: 88, debt: 0, efficiency: 90, ideology: "Monarquía", armySize: 65 } },
            "España": { flag: "🇪🇸", initialStats: { economy: 78, happiness: 75, stability: 80, relations: 80, population: 47, gdp: 1400, techDevelopment: 80, treasury: 140, diplomaticScore: 80, debt: 50, efficiency: 75, ideology: "Democracia", armySize: 130 } },
            "Estados Unidos": { flag: "🇺🇸", initialStats: { economy: 92, happiness: 75, stability: 85, relations: 88, population: 330, gdp: 25000, techDevelopment: 98, treasury: 2500, diplomaticScore: 88, debt: 0, efficiency: 90, ideology: "Capitalismo", armySize: 1300 } },
            "Etiopía": { flag: "🇪🇹", initialStats: { economy: 35, happiness: 30, stability: 25, relations: 30, population: 120, gdp: 100, techDevelopment: 15, treasury: 10, diplomaticScore: 30, debt: 15, efficiency: 30, ideology: "Autoritarismo", armySize: 160 } },
            "Filipinas": { flag: "🇵🇭", initialStats: { economy: 60, happiness: 55, stability: 50, relations: 55, population: 115, gdp: 400, techDevelopment: 45, treasury: 40, diplomaticScore: 55, debt: 25, efficiency: 50, ideology: "Democracia", armySize: 150 } },
            "Finlandia": { flag: "🇫🇮", initialStats: { economy: 85, happiness: 95, stability: 95, relations: 90, population: 5, gdp: 300, techDevelopment: 95, treasury: 30, diplomaticScore: 90, debt: 0, efficiency: 95, ideology: "Socialismo", armySize: 20 } },
            "Francia": { flag: "🇫🇷", initialStats: { economy: 85, happiness: 80, stability: 85, relations: 90, population: 65, gdp: 2800, techDevelopment: 90, treasury: 280, diplomaticScore: 90, debt: 0, efficiency: 88, ideology: "Democracia", armySize: 200 } },
            "Ghana": { flag: "🇬🇭", initialStats: { economy: 45, happiness: 40, stability: 35, relations: 40, population: 32, gdp: 80, techDevelopment: 20, treasury: 8, diplomaticScore: 40, debt: 10, efficiency: 40, ideology: "Democracia", armySize: 15 } },
            "Grecia": { flag: "🇬🇷", initialStats: { economy: 60, happiness: 65, stability: 60, relations: 65, population: 10, gdp: 200, techDevelopment: 60, treasury: 20, diplomaticScore: 65, debt: 30, efficiency: 60, ideology: "Democracia", armySize: 140 } },
            "Guatemala": { flag: "🇬🇹", initialStats: { economy: 40, happiness: 45, stability: 35, relations: 40, population: 18, gdp: 90, techDevelopment: 25, treasury: 9, diplomaticScore: 40, debt: 10, efficiency: 35, ideology: "Democracia", armySize: 30 } },
            "Guinea": { flag: "🇬🇳", initialStats: { economy: 30, happiness: 25, stability: 20, relations: 25, population: 13, gdp: 15, techDevelopment: 5, treasury: 1, diplomaticScore: 25, debt: 5, efficiency: 25, ideology: "Militarismo", armySize: 15 } },
            "Honduras": { flag: "🇭🇳", initialStats: { economy: 35, happiness: 40, stability: 30, relations: 35, population: 10, gdp: 25, techDevelopment: 20, treasury: 2, diplomaticScore: 35, debt: 5, efficiency: 30, ideology: "Democracia", armySize: 20 } },
            "Hungría": { flag: "🇭🇺", initialStats: { economy: 65, happiness: 60, stability: 70, relations: 65, population: 9, gdp: 180, techDevelopment: 60, treasury: 18, diplomaticScore: 65, debt: 10, efficiency: 60, ideology: "Democracia", armySize: 20 } },
            "India": { flag: "🇮🇳", initialStats: { economy: 80, happiness: 60, stability: 70, relations: 70, population: 1400, gdp: 3500, techDevelopment: 75, treasury: 350, diplomaticScore: 70, debt: 0, efficiency: 70, ideology: "Democracia", armySize: 1450 } },
            "Indonesia": { flag: "🇮🇩", initialStats: { economy: 70, happiness: 65, stability: 60, relations: 65, population: 275, gdp: 1200, techDevelopment: 60, treasury: 120, diplomaticScore: 65, debt: 0, efficiency: 65, ideology: "Democracia", armySize: 400 } },
            "Irán": { flag: "🇮🇷", initialStats: { economy: 50, happiness: 40, stability: 70, relations: 30, population: 88, gdp: 350, techDevelopment: 50, treasury: 35, diplomaticScore: 30, debt: 20, efficiency: 45, ideology: "Teocracia", armySize: 600 } },
            "Irak": { flag: "🇮🇶", initialStats: { economy: 40, happiness: 30, stability: 25, relations: 30, population: 43, gdp: 200, techDevelopment: 20, treasury: 20, diplomaticScore: 30, debt: 15, efficiency: 30, ideology: "Autoritarismo", armySize: 200 } },
            "Irlanda": { flag: "🇮🇪", initialStats: { economy: 90, happiness: 85, stability: 90, relations: 90, population: 5, gdp: 500, techDevelopment: 90, treasury: 50, diplomaticScore: 90, debt: 0, efficiency: 90, ideology: "Democracia", armySize: 10 } },
            "Israel": { flag: "🇮🇱", initialStats: { economy: 85, happiness: 70, stability: 75, relations: 60, population: 9, gdp: 500, techDevelopment: 95, treasury: 50, diplomaticScore: 60, debt: 0, efficiency: 80, ideology: "Democracia", armySize: 170 } },
            "Italia": { flag: "🇮🇹", initialStats: { economy: 78, happiness: 70, stability: 75, relations: 80, population: 59, gdp: 2100, techDevelopment: 80, treasury: 210, diplomaticScore: 80, debt: 50, efficiency: 75, ideology: "Democracia", armySize: 170 } },
            "Jamaica": { flag: "🇯🇲", initialStats: { economy: 40, happiness: 65, stability: 50, relations: 55, population: 3, gdp: 15, techDevelopment: 30, treasury: 1, diplomaticScore: 55, debt: 5, efficiency: 50, ideology: "Democracia", armySize: 5 } },
            "Japón": { flag: "🇯🇵", initialStats: { economy: 90, happiness: 80, stability: 90, relations: 85, population: 125, gdp: 4200, techDevelopment: 98, treasury: 420, diplomaticScore: 85, debt: 0, efficiency: 95, ideology: "Democracia", armySize: 250 } },
            "Jordania": { flag: "🇯🇴", initialStats: { economy: 50, happiness: 55, stability: 60, relations: 55, population: 11, gdp: 50, techDevelopment: 40, treasury: 5, diplomaticScore: 55, debt: 10, efficiency: 50, ideology: "Monarquía", armySize: 100 } },
            "Kazajistán": { flag: "🇰🇿", initialStats: { economy: 60, happiness: 55, stability: 65, relations: 60, population: 19, gdp: 200, techDevelopment: 50, treasury: 20, diplomaticScore: 60, debt: 10, efficiency: 55, ideology: "Autoritarismo", armySize: 40 } },
            "Kenia": { flag: "🇰🇪", initialStats: { economy: 45, happiness: 40, stability: 35, relations: 40, population: 56, gdp: 110, techDevelopment: 25, treasury: 11, diplomaticScore: 40, debt: 15, efficiency: 35, ideology: "Democracia", armySize: 25 } },
            "Kuwait": { flag: "🇰🇼", initialStats: { economy: 85, happiness: 70, stability: 80, relations: 75, population: 4, gdp: 180, techDevelopment: 70, treasury: 20, diplomaticScore: 75, debt: 0, efficiency: 75, ideology: "Monarquía", armySize: 20 } },
            "Laos": { flag: "🇱🇦", initialStats: { economy: 35, happiness: 40, stability: 30, relations: 35, population: 7, gdp: 20, techDevelopment: 10, treasury: 2, diplomaticScore: 35, debt: 5, efficiency: 30, ideology: "Comunismo", armySize: 30 } },
            "Líbano": { flag: "🇱🇧", initialStats: { economy: 20, happiness: 15, stability: 10, relations: 15, population: 5, gdp: 20, techDevelopment: 20, treasury: 1, diplomaticScore: 15, debt: 10, efficiency: 20, ideology: "Democracia", armySize: 70 } },
            "Libia": { flag: "🇱🇾", initialStats: { economy: 30, happiness: 20, stability: 15, relations: 20, population: 7, gdp: 30, techDevelopment: 10, treasury: 3, diplomaticScore: 20, debt: 10, efficiency: 25, ideology: "Autoritarismo", armySize: 30 } },
            "Madagascar": { flag: "🇲🇬", initialStats: { economy: 25, happiness: 30, stability: 20, relations: 25, population: 29, gdp: 15, techDevelopment: 5, treasury: 1, diplomaticScore: 25, debt: 5, efficiency: 20, ideology: "Democracia", armySize: 20 } },
            "Malasia": { flag: "🇲🇾", initialStats: { economy: 70, happiness: 65, stability: 70, relations: 70, population: 33, gdp: 450, techDevelopment: 70, treasury: 45, diplomaticScore: 70, debt: 0, efficiency: 70, ideology: "Monarquía", armySize: 110 } },
            "Malí": { flag: "🇲🇱", initialStats: { economy: 20, happiness: 15, stability: 10, relations: 15, population: 22, gdp: 20, techDevelopment: 5, treasury: 2, diplomaticScore: 15, debt: 5, efficiency: 20, ideology: "Militarismo", armySize: 15 } },
            "Marruecos": { flag: "🇲🇦", initialStats: { economy: 55, happiness: 50, stability: 50, relations: 55, population: 37, gdp: 130, techDevelopment: 40, treasury: 13, diplomaticScore: 55, debt: 10, efficiency: 50, ideology: "Monarquía", armySize: 200 } },
            "México": { flag: "🇲🇽", initialStats: { economy: 70, happiness: 65, stability: 60, relations: 65, population: 128, gdp: 1300, techDevelopment: 65, treasury: 130, diplomaticScore: 65, debt: 50, efficiency: 60, ideology: "Democracia", armySize: 270 } },
            "Mongolia": { flag: "🇲🇳", initialStats: { economy: 40, happiness: 50, stability: 55, relations: 50, population: 3, gdp: 15, techDevelopment: 30, treasury: 1, diplomaticScore: 50, debt: 5, efficiency: 45, ideology: "Democracia", armySize: 10 } },
            "Mozambique": { flag: "🇲🇿", initialStats: { economy: 30, happiness: 25, stability: 20, relations: 25, population: 32, gdp: 20, techDevelopment: 5, treasury: 2, diplomaticScore: 25, debt: 5, efficiency: 25, ideology: "Socialismo", armySize: 15 } },
            "Myanmar": { flag: "🇲🇲", initialStats: { economy: 35, happiness: 25, stability: 20, relations: 25, population: 55, gdp: 60, techDevelopment: 10, treasury: 6, diplomaticScore: 25, debt: 10, efficiency: 25, ideology: "Militarismo", armySize: 400 } },
            "Nepal": { flag: "🇳🇵", initialStats: { economy: 35, happiness: 40, stability: 30, relations: 35, population: 30, gdp: 40, techDevelopment: 15, treasury: 4, diplomaticScore: 35, debt: 5, efficiency: 30, ideology: "Democracia", armySize: 100 } },
            "Nicaragua": { flag: "🇳🇮", initialStats: { economy: 30, happiness: 35, stability: 25, relations: 30, population: 6, gdp: 15, techDevelopment: 15, treasury: 1, diplomaticScore: 30, debt: 5, efficiency: 25, ideology: "Autoritarismo", armySize: 15 } },
            "Níger": { flag: "🇳🇪", initialStats: { economy: 20, happiness: 15, stability: 10, relations: 15, population: 26, gdp: 10, techDevelopment: 5, treasury: 1, diplomaticScore: 15, debt: 5, efficiency: 20, ideology: "Militarismo", armySize: 10 } },
            "Nigeria": { flag: "🇳🇬", initialStats: { economy: 60, happiness: 40, stability: 30, relations: 40, population: 220, gdp: 500, techDevelopment: 30, treasury: 50, diplomaticScore: 40, debt: 30, efficiency: 40, ideology: "Democracia", armySize: 200 } },
            "Noruega": { flag: "🇳🇴", initialStats: { economy: 90, happiness: 95, stability: 95, relations: 90, population: 5, gdp: 550, techDevelopment: 95, treasury: 60, diplomaticScore: 90, debt: 0, efficiency: 95, ideology: "Socialismo", armySize: 25 } },
            "Nueva Zelanda": { flag: "🇳🇿", initialStats: { economy: 85, happiness: 90, stability: 90, relations: 88, population: 5, gdp: 250, techDevelopment: 85, treasury: 30, diplomaticScore: 88, debt: 0, efficiency: 90, ideology: "Democracia", armySize: 10 } },
            "Omán": { flag: "🇴🇲", initialStats: { economy: 70, happiness: 65, stability: 75, relations: 70, population: 5, gdp: 100, techDevelopment: 60, treasury: 10, diplomaticScore: 70, debt: 0, efficiency: 70, ideology: "Monarquía", armySize: 40 } },
            "Países Bajos": { flag: "🇳🇱", initialStats: { economy: 88, happiness: 85, stability: 90, relations: 90, population: 17, gdp: 1000, techDevelopment: 92, treasury: 100, diplomaticScore: 90, debt: 0, efficiency: 92, ideology: "Capitalismo", armySize: 40 } },
            "Pakistán": { flag: "🇵🇰", initialStats: { economy: 50, happiness: 40, stability: 35, relations: 45, population: 230, gdp: 350, techDevelopment: 30, treasury: 35, diplomaticScore: 45, debt: 40, efficiency: 40, ideology: "Democracia", armySize: 650 } },
            "Panamá": { flag: "🇵🇦", initialStats: { economy: 60, happiness: 65, stability: 60, relations: 65, population: 4, gdp: 70, techDevelopment: 50, treasury: 7, diplomaticScore: 65, debt: 5, efficiency: 60, ideology: "Capitalismo", armySize: 15 } },
            "Paraguay": { flag: "🇵🇾", initialStats: { economy: 45, happiness: 55, stability: 50, relations: 50, population: 7, gdp: 40, techDevelopment: 30, treasury: 4, diplomaticScore: 50, debt: 5, efficiency: 45, ideology: "Democracia", armySize: 20 } },
            "Perú": { flag: "🇵🇪", initialStats: { economy: 60, happiness: 60, stability: 50, relations: 55, population: 34, gdp: 250, techDevelopment: 50, treasury: 25, diplomaticScore: 55, debt: 20, efficiency: 50, ideology: "Democracia", armySize: 80 } },
            "Polonia": { flag: "🇵🇱", initialStats: { economy: 75, happiness: 70, stability: 75, relations: 70, population: 38, gdp: 700, techDevelopment: 70, treasury: 70, diplomaticScore: 70, debt: 0, efficiency: 70, ideology: "Capitalismo", armySize: 120 } },
            "Portugal": { flag: "🇵🇹", initialStats: { economy: 70, happiness: 75, stability: 75, relations: 75, population: 10, gdp: 300, techDevelopment: 70, treasury: 30, diplomaticScore: 75, debt: 10, efficiency: 70, ideology: "Democracia", armySize: 30 } },
            "Reino Unido": { flag: "🇬🇧", initialStats: { economy: 88, happiness: 80, stability: 85, relations: 88, population: 67, gdp: 3000, techDevelopment: 90, treasury: 300, diplomaticScore: 88, debt: 0, efficiency: 88, ideology: "Democracia", armySize: 150 } },
            "República Checa": { flag: "🇨🇿", initialStats: { economy: 75, happiness: 75, stability: 80, relations: 75, population: 10, gdp: 300, techDevelopment: 80, treasury: 30, diplomaticScore: 75, debt: 0, efficiency: 80, ideology: "Capitalismo", armySize: 25 } },
            "República Democrática del Congo": { flag: "🇨🇩", initialStats: { economy: 20, happiness: 10, stability: 5, relations: 10, population: 95, gdp: 60, techDevelopment: 5, treasury: 5, diplomaticScore: 10, debt: 20, efficiency: 15, ideology: "Autoritarismo", armySize: 130 } },
            "Rumanía": { flag: "🇷🇴", initialStats: { economy: 65, happiness: 60, stability: 65, relations: 60, population: 19, gdp: 300, techDevelopment: 60, treasury: 30, diplomaticScore: 60, debt: 10, efficiency: 60, ideology: "Democracia", armySize: 70 } },
            "Rusia": { flag: "🇷🇺", initialStats: { economy: 70, happiness: 50, stability: 80, relations: 40, population: 145, gdp: 2000, techDevelopment: 70, treasury: 200, diplomaticScore: 40, debt: 0, efficiency: 65, ideology: "Autoritarismo", armySize: 1150 } },
            "Ruanda": { flag: "🇷🇼", initialStats: { economy: 35, happiness: 40, stability: 30, relations: 35, population: 13, gdp: 15, techDevelopment: 10, treasury: 1, diplomaticScore: 35, debt: 5, efficiency: 30, ideology: "Autoritarismo", armySize: 30 } },
            "Senegal": { flag: "🇸🇳", initialStats: { economy: 40, happiness: 35, stability: 30, relations: 35, population: 17, gdp: 30, techDevelopment: 15, treasury: 3, diplomaticScore: 35, debt: 5, efficiency: 30, ideology: "Democracia", armySize: 15 } },
            "Serbia": { flag: "🇷🇸", initialStats: { economy: 55, happiness: 50, stability: 55, relations: 50, population: 7, gdp: 70, techDevelopment: 45, treasury: 7, diplomaticScore: 50, debt: 10, efficiency: 50, ideology: "Democracia", armySize: 30 } },
            "Singapur": { flag: "🇸🇬", initialStats: { economy: 98, happiness: 90, stability: 95, relations: 95, population: 5, gdp: 400, techDevelopment: 98, treasury: 50, diplomaticScore: 95, debt: 0, efficiency: 98, ideology: "Capitalismo", armySize: 70 } },
            "Siria": { flag: "🇸🇾", initialStats: { economy: 10, happiness: 5, stability: 5, relations: 5, population: 20, gdp: 10, techDevelopment: 5, treasury: 0, diplomaticScore: 5, debt: 50, efficiency: 10, ideology: "Autoritarismo", armySize: 150 } },
            "Somalia": { flag: "🇸🇴", initialStats: { economy: 5, happiness: 5, stability: 5, relations: 5, population: 17, gdp: 5, techDevelopment: 1, diplomaticScore: 5, debt: 20, efficiency: 5, ideology: "Anarquía", armySize: 20 } },
            "Sri Lanka": { flag: "🇱🇰", initialStats: { economy: 40, happiness: 35, stability: 30, relations: 35, population: 22, gdp: 80, techDevelopment: 20, treasury: 8, diplomaticScore: 35, debt: 15, efficiency: 30, ideology: "Democracia", armySize: 250 } },
            "Sudáfrica": { flag: "🇿🇦", initialStats: { economy: 65, happiness: 55, stability: 50, relations: 55, population: 60, gdp: 400, techDevelopment: 55, treasury: 40, diplomaticScore: 55, debt: 20, efficiency: 50, ideology: "Democracia", armySize: 70 } },
            "Sudán": { flag: "🇸🇩", initialStats: { economy: 20, happiness: 15, stability: 10, relations: 15, population: 45, gdp: 30, techDevelopment: 5, treasury: 2, diplomaticScore: 15, debt: 10, efficiency: 15, ideology: "Militarismo", armySize: 200 } },
            "Suecia": { flag: "🇸🇪", initialStats: { economy: 88, happiness: 90, stability: 92, relations: 90, population: 10, gdp: 600, techDevelopment: 95, treasury: 60, diplomaticScore: 90, debt: 0, efficiency: 92, ideology: "Socialismo", armySize: 20 } },
            "Suiza": { flag: "🇨🇭", initialStats: { economy: 95, happiness: 95, stability: 98, relations: 95, population: 8, gdp: 800, techDevelopment: 98, treasury: 100, diplomaticScore: 95, debt: 0, efficiency: 98, ideology: "Capitalismo", armySize: 20 } },
            "Tailandia": { flag: "🇹🇭", initialStats: { economy: 70, happiness: 65, stability: 60, relations: 65, population: 70, gdp: 500, techDevelopment: 60, treasury: 50, diplomaticScore: 65, debt: 0, efficiency: 65, ideology: "Monarquía", armySize: 300 } },
            "Taiwán": { flag: "🇹🇼", initialStats: { economy: 90, happiness: 80, stability: 85, relations: 70, population: 23, gdp: 800, techDevelopment: 95, treasury: 80, diplomaticScore: 70, debt: 0, efficiency: 90, ideology: "Capitalismo", armySize: 200 } },
            "Tanzania": { flag: "🇹🇿", initialStats: { economy: 35, happiness: 30, stability: 25, relations: 30, population: 65, gdp: 70, techDevelopment: 10, treasury: 7, diplomaticScore: 30, debt: 10, efficiency: 30, ideology: "Socialismo", armySize: 30 } },
            "Túnez": { flag: "🇹🇳", initialStats: { economy: 50, happiness: 45, stability: 40, relations: 45, population: 12, gdp: 50, techDevelopment: 35, treasury: 5, diplomaticScore: 45, debt: 10, efficiency: 40, ideology: "Democracia", armySize: 40 } },
            "Turquía": { flag: "🇹🇷", initialStats: { economy: 65, happiness: 55, stability: 60, relations: 55, population: 85, gdp: 800, techDevelopment: 60, treasury: 80, diplomaticScore: 55, debt: 20, efficiency: 55, ideology: "Autoritarismo", armySize: 425 } },
            "Ucrania": { flag: "🇺🇦", initialStats: { economy: 30, happiness: 20, stability: 15, relations: 20, population: 40, gdp: 150, techDevelopment: 30, treasury: 15, diplomaticScore: 20, debt: 30, efficiency: 25, ideology: "Democracia", armySize: 700 } },
            "Uganda": { flag: "🇺🇬", initialStats: { economy: 30, happiness: 25, stability: 20, relations: 25, population: 45, gdp: 40, techDevelopment: 10, treasury: 4, diplomaticScore: 25, debt: 10, efficiency: 25, ideology: "Autoritarismo", armySize: 45 } },
            "Uruguay": { flag: "🇺🇾", initialStats: { economy: 60, happiness: 70, stability: 65, relations: 65, population: 3, gdp: 60, techDevelopment: 55, treasury: 6, diplomaticScore: 65, debt: 0, efficiency: 65, ideology: "Democracia", armySize: 20 } },
            "Uzbekistán": { flag: "🇺🇿", initialStats: { economy: 50, happiness: 45, stability: 55, relations: 50, population: 35, gdp: 80, techDevelopment: 35, treasury: 8, diplomaticScore: 50, debt: 10, efficiency: 45, ideology: "Autoritarismo", armySize: 50 } },
            "Venezuela": { flag: "🇻🇪", initialStats: { economy: 10, happiness: 5, stability: 5, relations: 5, population: 28, gdp: 50, techDevelopment: 10, treasury: 0, diplomaticScore: 5, debt: 100, efficiency: 10, ideology: "Comunismo", armySize: 120 } },
            "Vietnam": { flag: "🇻🇳", initialStats: { economy: 70, happiness: 65, stability: 60, relations: 65, population: 98, gdp: 400, techDevelopment: 60, treasury: 40, diplomaticScore: 65, debt: 0, efficiency: 60, ideology: "Comunismo", armySize: 480 } },
            "Yemen": { flag: "🇾🇪", initialStats: { economy: 5, happiness: 5, stability: 5, relations: 5, population: 33, gdp: 10, techDevelopment: 1, diplomaticScore: 5, debt: 50, efficiency: 5, ideology: "Anarquía", armySize: 50 } },
            "Zimbabue": { flag: "🇿🇼", initialStats: { economy: 15, happiness: 10, stability: 10, relations: 10, population: 16, gdp: 20, techDevelopment: 5, treasury: 1, diplomaticScore: 10, debt: 10, efficiency: 10, ideology: "Autoritarismo", armySize: 30 } },
            // Default for any other country not explicitly listed
            "Default": { flag: "🌐", initialStats: { economy: 70, happiness: 70, stability: 70, relations: 70, population: 50, gdp: 500, techDevelopment: 50, treasury: 50, diplomaticScore: 70, debt: 0, efficiency: 70, ideology: "Democracia", armySize: 100 } }
        };

        // Extract only country names for the sorted list
        const countriesList = Object.keys(countryData).filter(country => country !== "Default").sort();

        // Ideology Definitions
        const ideologies = {
            "Capitalismo": {
                description: "Un sistema económico basado en la propiedad privada de los medios de producción y la operación para obtener ganancias. Promueve la libertad económica y la competencia.",
                consequences: { economy: +15, techDevelopment: +10, happiness: -5, stability: -5, relations: +5, efficiency: +5 }
            },
            "Comunismo": {
                description: "Un sistema socioeconómico en el que los medios de producción son propiedad común y la sociedad es sin clases y sin estado. Busca la igualdad y la justicia social.",
                consequences: { economy: -15, happiness: +15, stability: +10, relations: -10, techDevelopment: -5, efficiency: -10 }
            },
            "Socialismo": {
                description: "Un sistema social y económico caracterizado por la propiedad social de los medios de producción y la autogestión de los trabajadores, así como por teorías políticas y movimientos asociados con él.",
                consequences: { economy: -5, happiness: +10, stability: +5, relations: +5, treasury: -10, efficiency: -5 }
            },
            "Fascismo": {
                description: "Una ideología política ultranacionalista autoritaria. Se caracteriza por un poder dictatorial, la supresión de la oposición y una fuerte regimentación de la sociedad y la economía.",
                consequences: { economy: +5, happiness: -20, stability: +20, relations: -20, diplomaticScore: -15, efficiency: +10 }
            },
            "Democracia": {
                description: "Un sistema de gobierno en el que el poder reside en el pueblo, que lo ejerce directamente o a través de representantes libremente elegidos. Promueve la libertad y los derechos individuales.",
                consequences: { economy: +5, happiness: +10, stability: 0, relations: +10, diplomaticScore: +10, efficiency: -5 }
            },
            "Monarquía": {
                description: "Un sistema de gobierno en el que el jefe de estado es un monarca, que generalmente hereda su posición y la mantiene de por vida. Puede ser absoluta o constitucional.",
                consequences: { economy: 0, happiness: +5, stability: +10, relations: 0, efficiency: +5 }
            },
            "Autoritarismo": {
                description: "Un sistema de gobierno caracterizado por un fuerte poder central y una limitada libertad política. El poder se concentra en una sola persona o un pequeño grupo.",
                consequences: { economy: +5, happiness: -10, stability: +15, relations: -5, efficiency: +10 }
            },
            "Teocracia": {
                description: "Un sistema de gobierno en el que los gobernantes son considerados divinamente guiados o son miembros de un clero. La ley religiosa es la ley del estado.",
                consequences: { economy: -5, happiness: +5, stability: +5, relations: -5, techDevelopment: -10 }
            },
            "Anarquía": {
                description: "La ausencia de gobierno y autoridad. En este estado, no hay leyes ni orden, lo que a menudo lleva al caos.",
                consequences: { economy: -30, happiness: -30, stability: -50, relations: -30, techDevelopment: -20, treasury: -50, diplomaticScore: -30, efficiency: -50 }
            }
        };


        // --- Función Definiciones ---

        // Function to add a news entry to the news feed
        function addNewsEntry(title, description, consequences = {}) {
            const newsItemDiv = document.createElement('div');
            newsItemDiv.className = 'news-item';

            const titleSpan = document.createElement('span');
            titleSpan.className = 'news-item-title';
            titleSpan.textContent = title;
            newsItemDiv.appendChild(titleSpan);

            const descriptionP = document.createElement('p');
            descriptionP.className = 'news-item-description';
            descriptionP.textContent = description;
            newsItemDiv.appendChild(descriptionP);

            const impactDiv = document.createElement('div');
            impactDiv.className = 'news-item-impact';

            const statMapping = {
                economy: { name: "Economía", format: 0, unit: "%" },
                happiness: { name: "Felicidad", format: 0, unit: "%" },
                stability: { name: "Estabilidad", format: 0, unit: "%" },
                relations: { name: "Relaciones Globales", format: 0, unit: "%" },
                population: { name: "Población", format: 1, unit: "M" },
                gdp: { name: "PIB", format: 0, unit: "B USD" },
                techDevelopment: { name: "Tecnología", format: 0, unit: "%" },
                treasury: { name: "Tesorería", format: 2, unit: "B USD" },
                diplomaticScore: { name: "Diplomacia", format: 0, unit: "%" },
                debt: { name: "Deuda", format: 2, unit: "B USD" },
                inflationRate: { name: "Inflación", format: 2, unit: "%" },
                efficiency: { name: "Eficiencia", format: 0, unit: "%" },
                bilateralRelations: { name: "Relaciones Bilaterales", format: 0, unit: "%" }, // For specific country relations
                armySize: { name: "Ejército", format: 0, unit: "k" } // New army size mapping
            };

            let hasImpact = false;
            for (const key in consequences) {
                if (statMapping[key] && consequences[key] !== 0) { // Only show non-zero impacts
                    hasImpact = true;
                    const change = consequences[key];
                    const statInfo = statMapping[key];
                    const changeText = change > 0 ? `+${change.toFixed(statInfo.format)}` : `${change.toFixed(statInfo.format)}`;
                    const changeClass = change > 0 ? 'positive' : 'negative';

                    const impactSpan = document.createElement('span');
                    impactSpan.className = changeClass;
                    impactSpan.textContent = `${statInfo.name}: ${changeText}${statInfo.unit}`;
                    impactDiv.appendChild(impactSpan);
                }
            }

            if (hasImpact) {
                newsItemDiv.appendChild(impactDiv);
            } else {
                const noImpactSpan = document.createElement('span');
                noImpactSpan.className = 'neutral';
                noImpactSpan.textContent = 'Sin cambios significativos';
                impactDiv.appendChild(noImpactSpan);
                newsItemDiv.appendChild(impactDiv);
            }


            newsFeedContainer.prepend(newsItemDiv); // Add to the top (most recent first)
            // Keep only the last 10 news items
            while (newsFeedContainer.children.length > 10) {
                newsFeedContainer.removeChild(newsFeedContainer.lastChild);
            }
        }

        // Function to apply a temporary animation to a stat value
        function animateStatChange(elementId) {
            const element = document.getElementById(elementId);
            if (element) {
                element.classList.remove('stat-value-changed'); // Reset animation
                void element.offsetWidth; // Trigger reflow
                element.classList.add('stat-value-changed'); // Add animation
            }
        }

        // Función para poblar el selector de países (para la selección inicial del país)
        function populateCountrySelect(filterText = '') {
            countrySelect.innerHTML = '<option value="" disabled selected>Selecciona un país</option>';
            const lowerCaseFilter = filterText.toLowerCase();

            const filteredCountries = countriesList.filter(country =>
                country.toLowerCase().includes(lowerCaseFilter)
            );

            filteredCountries.forEach(country => {
                const option = document.createElement('option');
                option.value = country;
                option.textContent = country;
                countrySelect.appendChild(option);
            });
        }

        // Función para poblar el selector de países en el modal de diplomacia
        function populateCountrySelectForDiplomacy() {
            diplomacyCountrySelect.innerHTML = '<option value="" disabled selected>Selecciona una nación</option>';
            countriesList.forEach(country => {
                const option = document.createElement('option');
                option.value = country;
                option.textContent = country;
                diplomacyCountrySelect.appendChild(option);
            });
            // Set initial display for bilateral relations
            updateBilateralRelationsDisplay();
        }

        // Function to update the bilateral relations display in the diplomacy modal
        function updateBilateralRelationsDisplay() {
            const selectedCountry = diplomacyCountrySelect.value;
            if (selectedCountry && countryData[selectedCountry]) {
                const otherCountryStats = countryData[selectedCountry].initialStats;
                selectedDiplomacyCountryName.textContent = selectedCountry;
                bilateralRelationsValue.textContent = playerRelations.get(selectedCountry);

                // Display other country's stats
                selectedCountryNameStats.textContent = selectedCountry;
                otherCountryEconomy.textContent = otherCountryStats.economy.toFixed(0);
                otherCountryHappiness.textContent = otherCountryStats.happiness.toFixed(0);
                otherCountryStability.textContent = otherCountryStats.stability.toFixed(0);
                otherCountryTech.textContent = otherCountryStats.techDevelopment.toFixed(0);
                otherCountryArmySize.textContent = otherCountryStats.armySize.toFixed(0);
                selectedCountryStatsSection.classList.remove('hidden'); // Show the stats section
            } else {
                selectedDiplomacyCountryName.textContent = "Nación";
                bilateralRelationsValue.textContent = "--";
                selectedCountryStatsSection.classList.add('hidden'); // Hide the stats section if no country is selected or data is missing
            }
        }

        // Función para mostrar una pantalla y ocultar las demás
        function showScreen(screenId) {
            mainMenuScreen.classList.add('hidden');
            countrySelectionScreen.classList.add('hidden');
            gameScreen.classList.add('hidden');
            gameFooter.classList.add('hidden'); // Ocultar el pie de página por defecto
            economicModal.classList.remove('show'); // Asegurarse de que los modales estén cerrados
            generalStatsModal.classList.remove('show');
            relationsModal.classList.remove('show');
            fundAllocationModal.classList.remove('show');
            confirmationModal.classList.remove('show'); // Ensure confirmation modal is hidden
            settingsModal.classList.remove('show'); // Ensure settings modal is hidden
            ideologyModal.classList.remove('show'); // Ensure ideology modal is hidden
            militaryModal.classList.remove('show'); // Ensure military modal is hidden

            if (screenId === 'mainMenuScreen') {
                mainMenuScreen.classList.remove('hidden');
            } else if (screenId === 'countrySelectionScreen') {
                countrySelectionScreen.classList.remove('hidden');
                countrySelectionScreen.classList.add('flex');
                countrySearchInput.value = ''; // Limpiar el campo de búsqueda al mostrar la pantalla
                populateCountrySelect(); // Rellenar el selector sin filtro inicial
            } else if (screenId === 'gameScreen') {
                gameScreen.classList.remove('hidden');
                gameScreen.classList.add('flex');
                gameFooter.classList.remove('hidden'); // Mostrar el pie de página en la pantalla del juego
            }
        }

        // Función para mostrar un mensaje al usuario (en lugar de alert)
        function showMessage(message) {
            messageText.textContent = message;
            messageBox.style.display = 'block';
        }

        // Función para ocultar el mensaje
        function hideMessage() {
            messageBox.style.display = 'none';
            // Después de cerrar el mensaje de Game Over, regresar al menú principal
            // Solo si el juego realmente terminó, para evitar regresar al menú si es un mensaje de advertencia
            if (economy <= 0 || happiness <= 0 || stability <= 0 || relations <= 0 || treasury < -100 || debt > 500) { // Added debt game over condition
                showScreen('mainMenuScreen');
            }
        }

        // Función para abrir el modal económico
        function openEconomicModal() {
            economicModal.classList.add('show');
        }

        // Función para cerrar el modal económico
        function closeEconomicModal() {
            economicModal.classList.remove('show');
        }

        // Función para abrir el modal de estadísticas generales
        function openGeneralStatsModal() {
            generalStatsModal.classList.add('show');
        }

        // Función para cerrar el modal de estadísticas generales
        function closeGeneralStatsModal() {
            generalStatsModal.classList.remove('show');
        }

        // Función para abrir el modal de relaciones
        function openRelationsModal() {
            populateCountrySelectForDiplomacy(); // Populate country select when opening relations modal
            relationsModal.classList.add('show');
        }

        // Función para cerrar el modal de relaciones
        function closeRelationsModal() {
            relationsModal.classList.remove('show');
        }

        // Función para abrir el modal de asignación de fondos
        function openFundAllocationModal() {
            currentAnnualBalanceDisplay.textContent = annualBalance.toFixed(2);
            populateFundAllocationInputs(); // Populate inputs and reset values
            fundAllocationModal.classList.add('show');
        }

        // Función para cerrar el modal de asignación de fondos
        function closeFundAllocationModal() {
            fundAllocationModal.classList.remove('show');
        }

        // Función para abrir el modal de configuración
        function openSettingsModal() {
            settingsModal.classList.add('show');
        }

        // Función para cerrar el modal de configuración
        function closeSettingsModal() {
            settingsModal.classList.remove('show');
        }

        // --- Funciones para el nuevo Modal de Ideologías ---
        function openIdeologyModal() {
            populateIdeologyCards();
            ideologyModal.classList.add('show');
            btnApplyIdeology.classList.add('hidden'); // Hide apply button initially
            selectedIdeologyKey = null; // Reset selection
        }

        function closeIdeologyModal() {
            ideologyModal.classList.remove('show');
        }

        function populateIdeologyCards() {
            ideologyCardsContainer.innerHTML = '';
            for (const key in ideologies) {
                if (key === currentIdeology) continue; // Don't show current ideology as an option

                const ideology = ideologies[key];
                const card = document.createElement('div');
                card.className = 'ideology-card';
                card.dataset.ideologyKey = key;

                let impactHtml = '';
                const statMapping = {
                    economy: { name: "Eco", format: 0, unit: "%" },
                    happiness: { name: "Fel", format: 0, unit: "%" },
                    stability: { name: "Est", format: 0, unit: "%" },
                    relations: { name: "Rel", format: 0, unit: "%" },
                    techDevelopment: { name: "Tec", format: 0, unit: "%" },
                    diplomaticScore: { name: "Dip", format: 0, unit: "%" },
                    efficiency: { name: "Efi", format: 0, unit: "%" }
                };

                for (const stat in ideology.consequences) {
                    if (statMapping[stat]) {
                        const change = ideology.consequences[stat];
                        const statInfo = statMapping[stat];
                        const changeText = change > 0 ? `+${change.toFixed(statInfo.format)}` : `${change.toFixed(statInfo.format)}`;
                        const changeClass = change > 0 ? 'positive' : (change < 0 ? 'negative' : 'neutral');
                        impactHtml += `<span class="${changeClass}">${statInfo.name}: ${changeText}${statInfo.unit}</span>`;
                    }
                }

                card.innerHTML = `
                    <h4>${key}</h4>
                    <p>${ideology.description}</p>
                    <div class="impact-list">${impactHtml}</div>
                `;
                ideologyCardsContainer.appendChild(card);

                card.addEventListener('click', () => {
                    clickSound.play();
                    // Remove selected class from all cards
                    document.querySelectorAll('.ideology-card').forEach(c => c.classList.remove('selected'));
                    // Add selected class to the clicked card
                    card.classList.add('selected');
                    selectedIdeologyKey = key;
                    btnApplyIdeology.classList.remove('hidden'); // Show apply button
                });
            }
        }

        function applyIdeologyChange() {
            if (!selectedIdeologyKey) {
                showMessage("Por favor, selecciona una ideología para aplicar.");
                return;
            }

            const cost = 10; // 10 Billion USD
            if (treasury < cost) {
                showMessage(`No tienes suficiente tesorería para cambiar a ${selectedIdeologyKey}. Necesitas ${cost.toFixed(2)} B USD.`);
                return;
            }

            const ideologyToApply = ideologies[selectedIdeologyKey];
            const message = `¿Estás seguro de cambiar la ideología de tu país a ${selectedIdeologyKey}? Esto costará ${cost.toFixed(2)} B USD y tendrá los siguientes impactos:`;

            let impactDetails = '';
            const statMapping = {
                economy: { name: "Economía", format: 0, unit: "%" },
                happiness: { name: "Felicidad", format: 0, unit: "%" },
                stability: { name: "Estabilidad", format: 0, unit: "%" },
                relations: { name: "Relaciones Globales", format: 0, unit: "%" },
                techDevelopment: { name: "Tecnología", format: 0, unit: "%" },
                diplomaticScore: { name: "Diplomacia", format: 0, unit: "%" },
                efficiency: { name: "Eficiencia", format: 0, unit: "%" }
            };

            for (const stat in ideologyToApply.consequences) {
                if (statMapping[stat]) {
                    const change = ideologyToApply.consequences[stat];
                    const statInfo = statMapping[stat];
                    const changeText = change > 0 ? `+${change.toFixed(statInfo.format)}` : `${change.toFixed(statInfo.format)}`;
                    impactDetails += `<br>${statInfo.name}: ${changeText}${statInfo.unit}`;
                }
            }

            showConfirmationModal("Confirmar Cambio de Ideología", `${message}${impactDetails}`, () => {
                const consequences = { ...ideologyToApply.consequences, treasury: -cost }; // Add cost to consequences
                const actualChanges = applyDecisionConsequences(consequences);
                currentIdeology = selectedIdeologyKey; // Update the ideology
                addNewsEntry("Cambio de Ideología", `Tu país ha adoptado la ideología de ${currentIdeology}.`, actualChanges);
                showMessage(`¡Tu país ahora es una ${currentIdeology}!`);
                closeIdeologyModal();
                updateStats(); // Update display immediately
            });
        }


        // --- Funciones para el nuevo Modal de Asignación de Fondos ---
        const fundAllocationCauses = [
            { name: "Educación 📚", id: "education", effects: { happiness: 15, techDevelopment: 25 } }, // Adjusted values
            { name: "Salud Pública 🏥", id: "publicHealth", effects: { happiness: 20, population: 0.5 } }, // Adjusted values
            { name: "Infraestructura 🏗️", id: "infrastructure", effects: { economy: 15, stability: 10 } }, // Adjusted values
            { name: "Defensa 🛡️", id: "defense", effects: { stability: 20, relations: -5 } }, // Adjusted values
            { name: "Investigación y Desarrollo 🔬", id: "research", effects: { techDevelopment: 30, economy: 5 } }, // Adjusted values
            { name: "Bienestar Social 👨‍👩‍👧‍👦", id: "socialWelfare", effects: { happiness: 20, stability: 10 } }, // Adjusted values
            { name: "Medio Ambiente 🌳", id: "environment", effects: { happiness: 10, relations: 10 } } // Adjusted values
        ];

        function populateFundAllocationInputs() {
            fundAllocationInputsContainer.innerHTML = '';
            fundAllocationCauses.forEach(cause => {
                const div = document.createElement('div');
                div.className = 'fund-input-group';
                div.innerHTML = `
                    <label for="fund-${cause.id}">${cause.name}</label>
                    <input type="range" id="fund-${cause.id}" min="0" max="100" value="0" class="fund-input">
                    <span class="percentage-value" id="fund-${cause.id}-value">0%</span>
                `;
                fundAllocationInputsContainer.appendChild(div);

                // Add event listener to update total percentage and individual value on input change
                const input = div.querySelector(`#fund-${cause.id}`);
                const valueSpan = div.querySelector(`#fund-${cause.id}-value`);
                input.addEventListener('input', (event) => {
                    let currentSliderValue = parseInt(event.target.value) || 0;
                    // Ensure currentSliderValue is not negative (redundant due to min="0" but good practice)
                    currentSliderValue = Math.max(0, currentSliderValue);

                    let sumOfOtherSliders = 0;
                    const allInputs = fundAllocationInputsContainer.querySelectorAll('.fund-input');
                    allInputs.forEach(otherInput => {
                        if (otherInput.id !== input.id) { // Sum up values of all other sliders
                            sumOfOtherSliders += parseInt(otherInput.value) || 0;
                        }
                    });

                    // Calculate the maximum allowed value for the current slider
                    const maxAllowedForCurrentSlider = 100 - sumOfOtherSliders;

                    // If the current slider's value exceeds its maximum allowed, cap it
                    if (currentSliderValue > maxAllowedForCurrentSlider) {
                        currentSliderValue = maxAllowedForCurrentSlider;
                        // Also ensure it's not negative if maxAllowedForCurrentSlider happens to be negative
                        currentSliderValue = Math.max(0, currentSliderValue);
                        event.target.value = currentSliderValue; // Update the actual slider position
                    }

                    valueSpan.textContent = `${currentSliderValue}%`;
                    updateTotalAllocatedPercentage();
                });
            });
            updateTotalAllocatedPercentage(); // Initial update
        }

        function getTotalAllocatedPercentage() {
            let total = 0;
            const inputs = fundAllocationInputsContainer.querySelectorAll('.fund-input');
            inputs.forEach(input => {
                total += parseInt(input.value) || 0;
            });
            return total;
        }

        function updateTotalAllocatedPercentage() {
            const total = getTotalAllocatedPercentage();
            // Ensure the displayed total is never negative, even if a bug somehow caused it.
            totalAllocatedValue.textContent = Math.max(0, total);

            // Update styling based on total percentage
            totalAllocatedPercentageDisplay.classList.remove('error', 'success');
            if (total !== 100) {
                totalAllocatedPercentageDisplay.classList.add('error');
            } else if (total === 100) {
                totalAllocatedPercentageDisplay.classList.add('success');
            }
        }

        function confirmFundAllocation() {
            let total = 0;
            const allocations = {};
            const inputs = fundAllocationInputsContainer.querySelectorAll('.fund-input');

            inputs.forEach(input => {
                const percentage = parseInt(input.value) || 0;
                total += percentage;
                allocations[input.id.replace('fund-', '')] = percentage;
            });

            if (total !== 100) { // Must be exactly 100%
                showMessage("¡Error de asignación! El porcentaje total asignado DEBE ser exactamente 100%.");
                return;
            }

            // Determine the overall economic impact factor from annualBalance.
            // This factor scales the effects: positive balance improves stats, negative balance worsens them.
            // We normalize by 100 to make the effects meaningful for typical annualBalance values (in Billions USD).
            const balanceImpactFactor = annualBalance / 100;
            const efficiencyFactor = efficiency / 100; // Efficiency scales the effectiveness of positive impacts

            const changes = {}; // To track changes for news log

            fundAllocationCauses.forEach(cause => {
                const percentage = allocations[cause.id];
                if (percentage > 0) {
                    // Calculate the proportional effect for this cause based on its defined effects,
                    // the percentage allocated, and the overall balance impact factor.
                    const allocatedEffectMultiplier = percentage / 100;

                    for (const stat in cause.effects) {
                        let change = cause.effects[stat] * allocatedEffectMultiplier * balanceImpactFactor;
                        // Apply efficiency to positive changes
                        if (change > 0) {
                            change *= efficiencyFactor;
                        }
                        changes[stat] = (changes[stat] || 0) + change;
                    }
                }
            });

            // Apply changes
            for (const stat in changes) {
                const change = changes[stat];
                switch (stat) {
                    case 'economy': economy += change; animateStatChange('economyValue'); break;
                    case 'happiness': happiness += change; animateStatChange('happinessValue'); break;
                    case 'stability': stability += change; animateStatChange('stabilityValue'); break;
                    case 'relations': relations += change; animateStatChange('relationsValue'); break;
                    case 'population': population += change; animateStatChange('populationValue'); break;
                    case 'gdp': gdp += change; animateStatChange('gdpValueModal'); break;
                    case 'techDevelopment': techDevelopment += change; animateStatChange('techDevelopmentValue'); break;
                    case 'treasury': treasury += change; animateStatChange('treasuryValueModal'); break;
                    case 'diplomaticScore': diplomaticScore += change; animateStatChange('diplomaticScoreValue'); break;
                    case 'debt': debt += change; animateStatChange('debtValueModal'); break;
                    case 'efficiency': efficiency += change; animateStatChange('efficiencyValue'); break;
                }
            }

            // Ensure stats stay within valid ranges after applying effects
            economy = Math.max(0, Math.min(100, economy));
            happiness = Math.max(0, Math.min(100, happiness));
            stability = Math.max(0, Math.min(100, stability));
            relations = Math.max(0, Math.min(100, relations));
            techDevelopment = Math.max(0, Math.min(100, techDevelopment));
            population = Math.max(1, population); // Minimum population of 1M
            diplomaticScore = Math.max(0, Math.min(100, diplomaticScore));
            efficiency = Math.max(0, Math.min(100, efficiency));

            // Update treasury with the net annual balance
            treasury += annualBalance;
            treasury = Math.round(treasury * 100) / 100;

            addNewsEntry("Asignación de Fondos Anual", "Se han asignado fondos a varios sectores del país.", changes);

            closeFundAllocationModal();
            updateStats();
        }

        // Función para verificar si el juego ha terminado
        function checkGameOver() {
            const isBankrupt = economy <= 0 || treasury < -100;
            const isOtherGameOver = happiness <= 0 || stability <= 0 || relations <= 0 || debt > 500;

            if (isBankrupt && unAidCount < 3) {
                offerUNAid();
                return true; // Indicate that a special game state is active, not a full game over yet
            } else if (isBankrupt || isOtherGameOver) { // If bankrupt and no more aid, or other conditions
                let gameOverMessage = "";
                if (economy <= 0) gameOverMessage = "¡Tu economía ha colapsado! El país ha caído en bancarrota. 📉";
                else if (happiness <= 0) gameOverMessage = "¡El pueblo está en rebelión! Has sido derrocado. ✊";
                else if (stability <= 0) gameOverMessage = "¡El país ha caído en el caos! La estabilidad se ha perdido. 💥";
                else if (relations <= 0) gameOverMessage = "¡Tu país ha sido aislado internacionalmente! 🚫";
                else if (treasury < -100) gameOverMessage = "¡El país está en una deuda insostenible con la tesorería vacía! Fin del juego. 💸";
                else if (debt > 500) gameOverMessage = "¡La deuda de tu país es insostenible! Fin del juego. 💸";


                showMessage(`${gameOverMessage} Fin del juego en el año ${year}.`);
                addNewsEntry("¡Fin del Juego!", gameOverMessage, {});
                return true;
            }
            return false;
        }

        // New function to offer UN aid
        function offerUNAid() {
            eventText.textContent = `¡Tu país está en bancarrota! La ONU ofrece un rescate de 10B USD. Te quedan ${3 - unAidCount} rescates. ¿Aceptas?`;
            optionsContainer.innerHTML = ''; // Clear previous options

            const acceptButton = document.createElement('button');
            acceptButton.className = 'button-option bg-green-600 hover:bg-green-700';
            acceptButton.textContent = 'Aceptar Ayuda de la ONU ✅';
            acceptButton.addEventListener('click', () => {
                clickSound.play();
                unAidCount++;
                const consequences = { treasury: 10, debt: 15, relations: -10, happiness: -5, stability: -5 };
                const actualChanges = applyDecisionConsequences(consequences);
                addNewsEntry("Ayuda de la ONU Aceptada", `Has recibido 10B USD de la ONU. Tu deuda ha aumentado.`, actualChanges);
                showMessage(`¡Ayuda de la ONU aceptada! Has recibido 10B USD. Te quedan ${3 - unAidCount} rescates.`);
                // After accepting aid, the game should continue to the next event or turn
                if (gameEventsQueue.length > 0) {
                    processNextEventInTurn();
                } else {
                    nextTurn(); // If no more events in queue, advance to next turn
                }
            });
            optionsContainer.appendChild(acceptButton);

            const rejectButton = document.createElement('button');
            rejectButton.className = 'button-option bg-red-600 hover:bg-red-700';
            rejectButton.textContent = 'Rechazar Ayuda y Terminar Juego ❌';
            rejectButton.addEventListener('click', () => {
                clickSound.play();
                // Force game over by setting a condition that will trigger it next check
                economy = 0; // Or any other condition to ensure game over
                checkGameOver(); // This will now trigger the final game over message
            });
            optionsContainer.appendChild(rejectButton);
        }

        // Función para actualizar las estadísticas en la interfaz (pantalla principal y modal)
        function updateStats() {
            // Estadísticas en la pantalla principal (Overview Stats)
            economyValue.textContent = economy.toFixed(0);
            happinessValue.textContent = happiness.toFixed(0);
            stabilityValue.textContent = stability.toFixed(0);
            relationsValue.textContent = relations.toFixed(0);
            populationValue.textContent = population.toFixed(1);
            techDevelopmentValue.textContent = techDevelopment.toFixed(0);
            diplomaticScoreValue.textContent = diplomaticScore.toFixed(0);
            efficiencyValue.textContent = efficiency.toFixed(0); // Update efficiency display
            ideologyValue.textContent = currentIdeology; // Update ideology display
            armySizeValue.textContent = armySize.toFixed(0); // Update army size display

            // Display year and month
            yearDisplay.textContent = year;
            monthDisplay.textContent = currentMonth;
            currentCountryDisplay.textContent = currentCountry;
            countryFlagEmoji.textContent = currentCountryFlagEmoji;

            // Estadísticas económicas (para el modal económico)
            gdpValueModal.textContent = gdp.toFixed(0);
            treasuryValueModal.textContent = treasury.toFixed(2);
            totalMoneyGeneratedValueModal.textContent = totalMoneyGenerated.toFixed(2);
            taxRevenueValueModal.textContent = taxRevenue.toFixed(2);
            industryRevenueValueModal.textContent = industryRevenue.toFixed(2);
            maintenanceCostsValueModal.textContent = maintenanceCosts.toFixed(2); // Display with 2 decimal places
            annualBalanceValueModal.textContent = annualBalance.toFixed(2);
            inflationRateValueModal.textContent = inflationRate.toFixed(2); // Display inflation rate
            debtValueModal.textContent = debt.toFixed(2); // Display debt
            currencyValueModal.textContent = `${(currencyValue * 100).toFixed(2)}%`; // Display currency value

            // Estadísticas generales (para el modal de estadísticas generales con barras)
            economyValueModal.textContent = economy.toFixed(0);
            happinessValueModal.textContent = happiness.toFixed(0);
            stabilityValueModal.textContent = stability.toFixed(0);
            relationsValueModal.textContent = relations.toFixed(0);
            populationValueModal.textContent = population.toFixed(1);
            techDevelopmentValueModal.textContent = techDevelopment.toFixed(0);
            diplomaticScoreValueModal.textContent = diplomaticScore.toFixed(0);
            efficiencyValueModal.textContent = efficiency.toFixed(0); // Update efficiency modal display
            ideologyValueModal.textContent = currentIdeology; // Update ideology modal display
            armySizeValueModal.textContent = armySize.toFixed(0); // Update army size modal display

            // Actualizar ancho y clase de color de las barras en el modal de estadísticas generales
            economyBarModal.style.width = `${economy}%`;
            economyBarModal.className = `stat-bar ${economy < 30 ? 'low-economy' : ''}`;

            happinessBarModal.style.width = `${happiness}%`;
            happinessBarModal.className = `stat-bar ${happiness < 30 ? 'low-happiness' : ''}`;

            stabilityBarModal.style.width = `${stability}%`;
            stabilityBarModal.className = `stat-bar ${stability < 30 ? 'low-stability' : ''}`;

            relationsBarModal.style.width = `${relations}%`;
            relationsBarModal.className = `stat-bar ${relations < 30 ? 'low-relations' : ''}`;

            efficiencyBarModal.style.width = `${efficiency}%`;
            efficiencyBarModal.className = `stat-bar ${efficiency < 30 ? 'low-efficiency' : ''}`;

            // Update bilateral relations display in diplomacy modal
            updateBilateralRelationsDisplay();

            // Update military modal display
            currentArmySizeDisplay.textContent = armySize.toFixed(0);
        }

        // --- Funciones de Acciones Diplomáticas ---

        function getSelectedDiplomacyCountry() {
            return diplomacyCountrySelect.value;
        }

        function getAcceptanceChance(baseChance, relationsFactor, currentBilateralRelation) {
            // Base chance + (currentBilateralRelation / 100) * relationsFactor
            // relationsFactor determines how much relations influence the chance
            return Math.min(1, baseChance + (currentBilateralRelation / 100) * relationsFactor);
        }

        function modifyBilateralRelations(targetCountry, amount) {
            let current = playerRelations.get(targetCountry) || 0; // Default to 0 if not set
            current = Math.max(0, Math.min(100, current + amount));
            playerRelations.set(targetCountry, current);
        }

        function breakRelations() {
            const targetCountry = getSelectedDiplomacyCountry();
            if (!targetCountry) {
                showMessage("Por favor, selecciona una nación para romper relaciones.");
                return;
            }
            const consequences = {
                relations: -(40 + Math.floor(Math.random() * 20)), // -40 to -59
                diplomaticScore: -(10 + Math.floor(Math.random() * 10)),
                stability: -5,
                economy: -5,
                bilateralRelations: -(40 + Math.floor(Math.random() * 20))
            };
            const actualChanges = applyDecisionConsequences(consequences);
            addNewsEntry("Relaciones Rotas", `Has roto relaciones con ${targetCountry}.`, actualChanges);
            closeRelationsModal();
            processNextEventInTurn();
        }

        function increaseRelations() {
            const targetCountry = getSelectedDiplomacyCountry();
            if (!targetCountry) {
                showMessage("Por favor, selecciona una nación para mejorar las relaciones.");
                return;
            }
            const currentBilateralRelation = playerRelations.get(targetCountry);
            const acceptanceChance = getAcceptanceChance(0.4, 0.5, currentBilateralRelation); // 40% base, +50% max from relations
            const consequences = {};
            if (Math.random() < acceptanceChance) {
                consequences.relations = 5; // Global relations slight boost
                consequences.diplomaticScore = (5 + Math.floor(Math.random() * 5));
                consequences.happiness = 5;
                consequences.bilateralRelations = 10 + Math.floor(Math.random() * 10); // +10 to +19
                const actualChanges = applyDecisionConsequences(consequences);
                addNewsEntry("Relaciones Mejoradas", `Has mejorado las relaciones con ${targetCountry}.`, actualChanges);
            } else {
                consequences.relations = -2; // Global relations slight hit
                consequences.bilateralRelations = -5; // Slight penalty for rejection
                const actualChanges = applyDecisionConsequences(consequences);
                addNewsEntry("Relaciones Mejoradas (Rechazado)", `Tu intento de mejorar relaciones con ${targetCountry} fue rechazado.`, actualChanges);
            }
            closeRelationsModal();
            processNextEventInTurn();
        }

        function makeTradeAgreement() {
            const targetCountry = getSelectedDiplomacyCountry();
            if (!targetCountry) {
                showMessage("Por favor, selecciona una nación para un acuerdo comercial.");
                return;
            }
            const currentBilateralRelation = playerRelations.get(targetCountry);
            const acceptanceChance = getAcceptanceChance(0.3, 0.6, currentBilateralRelation); // 30% base, +60% max from relations
            const consequences = {};
            if (Math.random() < acceptanceChance) {
                consequences.relations = 10; // Global relations boost
                consequences.diplomaticScore = (10 + Math.floor(Math.random() * 5));
                consequences.economy = (10 + Math.floor(Math.random() * 10));
                consequences.gdp = (50 + Math.floor(Math.random() * 50));
                consequences.treasury = (50 + Math.floor(Math.random() * 50)); // Add treasury gain
                consequences.bilateralRelations = 15 + Math.floor(Math.random() * 10); // +15 to +24
                const actualChanges = applyDecisionConsequences(consequences);
                addNewsEntry("Acuerdo Comercial", `Has firmado un acuerdo comercial con ${targetCountry}.`, actualChanges);
            } else {
                consequences.relations = -5; // Global relations hit
                consequences.bilateralRelations = -10; // Penalty for rejection
                const actualChanges = applyDecisionConsequences(consequences);
                addNewsEntry("Acuerdo Comercial (Rechazado)", `Tu propuesta de acuerdo comercial con ${targetCountry} fue rechazada.`, actualChanges);
            }
            closeRelationsModal();
            processNextEventInTurn();
        }

        function declareWar() {
            const targetCountry = getSelectedDiplomacyCountry();
            if (!targetCountry) {
                showMessage("Por favor, selecciona una nación para declarar la guerra.");
                return;
            }
            
            // Simulate opponent army size and combat power
            const opponentData = countryData[targetCountry] || countryData["Default"];
            const opponentArmySize = opponentData.initialStats.armySize + (Math.random() * 50 - 25); // Base + random fluctuation
            const opponentCombatPower = opponentArmySize * (opponentData.initialStats.stability / 100) * (opponentData.initialStats.techDevelopment / 100);

            // Player combat power
            const playerCombatPower = armySize * (efficiency / 100) * (techDevelopment / 100);

            // Calculate win probability based on combat power ratio
            let winProbability = 0.5; // Base 50% chance
            if (playerCombatPower > opponentCombatPower) {
                winProbability += (playerCombatPower - opponentCombatPower) / (playerCombatPower + opponentCombatPower) * 0.4; // Up to +40%
            } else {
                winProbability -= (opponentCombatPower - playerCombatPower) / (playerCombatPower + opponentCombatPower) * 0.4; // Up to -40%
            }
            winProbability = Math.max(0.1, Math.min(0.9, winProbability)); // Cap between 10% and 90%

            const outcome = Math.random(); // 0 to 1
            const consequences = {
                relations: -(20 + Math.floor(Math.random() * 10)), // Global relations significant hit
                diplomaticScore: -(20 + Math.floor(Math.random() * 10)),
                stability: -(15 + Math.floor(Math.random() * 10)),
                economy: -(20 + Math.floor(Math.random() * 20)),
                population: -(5 + Math.random() * 5),
                bilateralRelations: -(40 + Math.floor(Math.random() * 20)), // -40 to -59
                armySize: -(armySize * 0.2) // Always lose some troops
            };

            let warMessage = `Has declarado la guerra a ${targetCountry}.`;
            if (outcome < winProbability) { // Player wins
                consequences.gdp = 100 + Math.floor(Math.random() * 100);
                consequences.treasury = 50 + Math.floor(Math.random() * 50);
                consequences.happiness = 10;
                consequences.stability = 10;
                consequences.armySize = Math.max(consequences.armySize, -(armySize * 0.1)); // Less troop loss on win
                warMessage += " ¡Has ganado la guerra!";
            } else { // Player loses or stalemate
                consequences.gdp = -(50 + Math.floor(Math.random() * 50));
                consequences.treasury = -(20 + Math.floor(Math.random() * 30));
                consequences.happiness = -10;
                consequences.stability = -10;
                consequences.armySize = Math.min(consequences.armySize, -(armySize * 0.3)); // More troop loss on loss
                warMessage += " ¡La guerra ha sido un desastre!";
            }
            const actualChanges = applyDecisionConsequences(consequences);
            addNewsEntry("Guerra Declarada", warMessage, actualChanges);
            closeRelationsModal();
            processNextEventInTurn();
        }

        function sendGift() {
            const targetCountry = getSelectedDiplomacyCountry();
            if (!targetCountry) {
                showMessage("Por favor, selecciona una nación para enviar un regalo.");
                return;
            }
            const currentBilateralRelation = playerRelations.get(targetCountry);
            const acceptanceChance = getAcceptanceChance(0.7, 0.2, currentBilateralRelation); // 70% base, +20% max from relations (very likely to accept)
            const consequences = {};
            if (Math.random() < acceptanceChance) {
                consequences.relations = 3; // Global relations slight boost
                consequences.diplomaticScore = (2 + Math.floor(Math.random() * 3));
                consequences.bilateralRelations = 5 + Math.floor(Math.random() * 5); // +5 to +9
                const actualChanges = applyDecisionConsequences(consequences);
                addNewsEntry("Regalo Enviado", `Has enviado un regalo a ${targetCountry}.`, actualChanges);
            } else {
                const actualChanges = applyDecisionConsequences({}); // No changes if rejected
                addNewsEntry("Regalo Rechazado", `¡${targetCountry} ha rechazado tu regalo!`, actualChanges);
            }
            closeRelationsModal();
            processNextEventInTurn();
        }

        function formAlliance() {
            const targetCountry = getSelectedDiplomacyCountry();
            if (!targetCountry) {
                showMessage("Por favor, selecciona una nación para formar una alianza.");
                return;
            }
            const currentBilateralRelation = playerRelations.get(targetCountry);
            const acceptanceChance = getAcceptanceChance(0.1, 0.7, currentBilateralRelation); // 10% base, +70% max from relations (requires high relations)
            const consequences = {};
            if (Math.random() < acceptanceChance) {
                consequences.relations = 15; // Global relations significant boost
                consequences.diplomaticScore = (15 + Math.floor(Math.random() * 10));
                consequences.stability = 5;
                consequences.economy = 5;
                consequences.bilateralRelations = 20 + Math.floor(Math.random() * 10); // +20 to +29
                const actualChanges = applyDecisionConsequences(consequences);
                addNewsEntry("Alianza Formada", `Has formado una alianza con ${targetCountry}.`, actualChanges);
            } else {
                consequences.relations = -10; // Global relations significant hit
                consequences.bilateralRelations = -15; // Significant penalty for rejection
                const actualChanges = applyDecisionConsequences(consequences);
                addNewsEntry("Alianza Rechazada", `Tu propuesta de alianza con ${targetCountry} fue rechazada.`, actualChanges);
            }
            closeRelationsModal();
            processNextEventInTurn();
        }

        // Removed: makeScientificAgreement, culturalExchange, espionage, humanitarianAid functions

        // --- Confirmation Modal Functions ---
        function showConfirmationModal(title, message, onConfirm) {
            confirmationTitle.textContent = title;
            confirmationMessage.innerHTML = message; // Use innerHTML to allow line breaks
            onConfirmCallback = onConfirm; // Store the callback
            confirmationModal.classList.add('show');
        }

        function hideConfirmationModal() {
            confirmationModal.classList.remove('show');
            onConfirmCallback = null; // Clear the callback
        }

        // --- New Economic Actions (with confirmation) ---
        function handlePayDebt() {
            const amount = parseFloat(payDebtAmountInput.value);
            if (isNaN(amount) || amount <= 0) {
                showMessage("Por favor, ingresa un monto válido para pagar la deuda.");
                return;
            }
            if (treasury < amount) {
                showMessage("No tienes suficiente tesorería para pagar esa cantidad de deuda.");
                return;
            }
            if (debt <= 0) {
                showMessage("No tienes deuda que pagar.");
                return;
            }

            const actualPaid = Math.min(amount, debt);
            const message = `Pagarás ${actualPaid.toFixed(2)} B USD de deuda. Tu tesorería bajará y tu deuda se reducirá. Tu economía, estabilidad, relaciones y diplomacia mejorarán.`;

            showConfirmationModal("Confirmar Pago de Deuda", message, () => {
                const consequences = {
                    treasury: -actualPaid,
                    debt: -actualPaid,
                    economy: 5,
                    stability: 5,
                    relations: 3,
                    diplomaticScore: 5
                };
                const actualChanges = applyDecisionConsequences(consequences);
                addNewsEntry("Deuda Pagada", `Has pagado ${actualPaid.toFixed(2)} B USD de deuda.`, actualChanges);
                payDebtAmountInput.value = '';
            });
        }

        function handlePrintMoney() {
            const amount = parseFloat(printMoneyAmountInput.value);
            if (isNaN(amount) || amount <= 0) {
                showMessage("Por favor, ingresa un monto válido para imprimir dinero.");
                return;
            }

            const message = `Imprimirás ${amount.toFixed(2)} B USD. Tu tesorería subirá, pero la inflación aumentará y el valor de tu moneda, economía, felicidad y estabilidad disminuirán.`;

            showConfirmationModal("Confirmar Impresión de Dinero", message, () => {
                const consequences = {
                    treasury: amount,
                    totalMoneyGenerated: amount,
                    inflationRate: (amount / 50), // Significant increase
                    economy: -(amount / 100), // Economy hit
                    happiness: -(amount / 50), // More significant happiness hit
                    stability: -(amount / 200), // Stability hit
                };

                // Decrease currency value (calculated separately as it's not a direct stat change)
                const totalEconomicSize = gdp + treasury;
                if (totalEconomicSize > 0) {
                    currencyValue = Math.max(0.05, currencyValue - (amount / totalEconomicSize * 0.2)); // Ensure it doesn't go below 5%
                } else {
                    currencyValue = Math.max(0.05, currencyValue - 0.1); // Fallback if no economic size
                }

                const actualChanges = applyDecisionConsequences(consequences);
                addNewsEntry("Dinero Impreso", `Has impreso ${amount.toFixed(2)} B USD.`, actualChanges);
                showMessage(`Has impreso ${amount.toFixed(2)} B USD. El valor de tu moneda ha disminuido. Cuidado con la inflación y la felicidad de tu población.`);
                printMoneyAmountInput.value = '';
            });
        }

        function handleTakeLoan() {
            const amount = parseFloat(takeLoanAmountInput.value);
            if (isNaN(amount) || amount <= 0) {
                showMessage("Por favor, ingresa un monto válido para sacar un préstamo.");
                return;
            }

            const interestRate = 0.20; // 20% interest
            const interestAmount = amount * interestRate;
            const totalLoanWithInterest = amount + interestAmount;

            const message = `Sacarás un préstamo de ${amount.toFixed(2)} B USD. Tu tesorería subirá, pero tu deuda aumentará a ${totalLoanWithInterest.toFixed(2)} B USD (incluye 20% de intereses). Tus relaciones, estabilidad y economía disminuirán ligeramente.`;

            showConfirmationModal("Confirmar Solicitud de Préstamo", message, () => {
                const consequences = {
                    treasury: amount,
                    debt: totalLoanWithInterest,
                    relations: -5,
                    stability: -5,
                    economy: -3
                };
                const actualChanges = applyDecisionConsequences(consequences);
                addNewsEntry("Préstamo Solicitado", `Has sacado un préstamo de ${amount.toFixed(2)} B USD.`, actualChanges);
                showMessage(`Has sacado un préstamo de ${amount.toFixed(2)} B USD. Pagarás un 20% de intereses. Tu deuda ha aumentado a ${debt.toFixed(2)} B USD.`);
                takeLoanAmountInput.value = '';
            });
        }

        // --- Military Actions ---
        function openMilitaryModal() {
            militaryModal.classList.add('show');
            currentArmySizeDisplay.textContent = armySize.toFixed(0);
        }

        function closeMilitaryModal() {
            militaryModal.classList.remove('show');
        }

        function handleTrainArmy() {
            const amount = parseInt(trainArmyAmountInput.value);
            if (isNaN(amount) || amount <= 0) {
                showMessage("Por favor, ingresa una cantidad válida de tropas a entrenar.");
                return;
            }
            const costPerK = 0.5; // 0.5 Billion USD per 1k troops
            const totalCost = amount * costPerK;

            if (treasury < totalCost) {
                showMessage(`No tienes suficiente tesorería para entrenar ${amount}k tropas. Necesitas ${totalCost.toFixed(2)} B USD.`);
                return;
            }

            const message = `¿Estás seguro de entrenar ${amount}k tropas? Esto costará ${totalCost.toFixed(2)} B USD. Tu ejército aumentará y tu eficiencia militar mejorará ligeramente.`;
            showConfirmationModal("Confirmar Entrenamiento de Ejército", message, () => {
                const consequences = {
                    treasury: -totalCost,
                    armySize: amount,
                    efficiency: 2 // Small boost to efficiency due to better training
                };
                const actualChanges = applyDecisionConsequences(consequences);
                addNewsEntry("Ejército Entrenado", `Has entrenado ${amount}k tropas.`, actualChanges);
                showMessage(`Has entrenado ${amount}k tropas. Tu ejército ahora es de ${armySize.toFixed(0)}k.`);
                trainArmyAmountInput.value = '10'; // Reset input
            });
        }

        function handleHireMercenaries() {
            const amount = parseInt(hireMercenariesAmountInput.value);
            if (isNaN(amount) || amount <= 0) {
                showMessage("Por favor, ingresa una cantidad válida de mercenarios a contratar.");
                return;
            }
            const costPerK = 1.5; // 1.5 Billion USD per 1k mercenaries
            const totalCost = amount * costPerK;

            if (treasury < totalCost) {
                showMessage(`No tienes suficiente tesorería para contratar ${amount}k mercenarios. Necesitas ${totalCost.toFixed(2)} B USD.`);
                return;
            }

            const message = `¿Estás seguro de contratar ${amount}k mercenarios? Esto costará ${totalCost.toFixed(2)} B USD. Tu ejército aumentará rápidamente, pero podría afectar la felicidad y las relaciones internacionales.`;
            showConfirmationModal("Confirmar Contratación de Mercenarios", message, () => {
                const consequences = {
                    treasury: -totalCost,
                    armySize: amount,
                    happiness: -5, // Mercenaries might be unpopular
                    relations: -3 // Seen as aggressive/unstable
                };
                const actualChanges = applyDecisionConsequences(consequences);
                addNewsEntry("Mercenarios Contratados", `Has contratado ${amount}k mercenarios.`, actualChanges);
                showMessage(`Has contratado ${amount}k mercenarios. Tu ejército ahora es de ${armySize.toFixed(0)}k.`);
                hireMercenariesAmountInput.value = '5'; // Reset input
            });
        }

        function handleRecruitVolunteers() {
            const amount = parseInt(recruitVolunteersAmountInput.value);
            if (isNaN(amount) || amount <= 0) {
                showMessage("Por favor, ingresa una cantidad válida de voluntarios a reclutar.");
                return;
            }

            const message = `¿Estás seguro de reclutar ${amount}k voluntarios? Esto es gratis, pero las tropas estarán menos entrenadas y podría afectar la estabilidad.`;
            showConfirmationModal("Confirmar Reclutamiento de Voluntarios", message, () => {
                const consequences = {
                    armySize: amount,
                    stability: -5, // Untrained troops can be a liability
                    efficiency: -2 // Slight decrease in overall military efficiency
                };
                const actualChanges = applyDecisionConsequences(consequences);
                addNewsEntry("Voluntarios Reclutados", `Has reclutado ${amount}k voluntarios.`, actualChanges);
                showMessage(`Has reclutado ${amount}k voluntarios. Tu ejército ahora es de ${armySize.toFixed(0)}k.`);
                recruitVolunteersAmountInput.value = '20'; // Reset input
            });
        }


        // Function to advance to the next turn (year/quarter)
        function nextTurn() {
            if (checkGameOver()) { // Check game over first
                return; // If game over or UN aid offered, stop further processing
            }

            // Advance by 3 months
            currentMonth += 3;
            if (currentMonth > 12) {
                year++;
                currentMonth = 1; // Reset month for new year
            }

            const quarterlyChanges = {};

            // Apply general changes for the quarter (scaled for 3 months)
            let ecoChange = (Math.floor(Math.random() * 5) - 2) / 4; // +/- 0.5 per quarter
            let hapChange = (Math.floor(Math.random() * 3) - 1) / 4; // +/- 0.25 per quarter
            let stabChange = (Math.floor(Math.random() * 3) - 1) / 4;
            let relChange = (Math.floor(Math.random() * 3) - 1) / 4; // Global relations also fluctuate
            let popChange = (Math.floor(Math.random() * 2) - 0) / 4; // Small growth
            let gdpChange = (Math.floor(Math.random() * 10) - 5) / 4; // Small fluctuation
            let techChange = (Math.floor(Math.random() * 3) - 1) / 4;
            let effChange = (Math.floor(Math.random() * 3) - 1) / 4; // Efficiency fluctuation
            let armyChange = (Math.floor(Math.random() * 2) - 1) / 4; // Natural army fluctuation

            // Apply interdependencies
            if (happiness < 50) stabChange -= 0.5; // Low happiness hurts stability
            if (stability < 50) ecoChange -= 0.5; // Low stability hurts economy
            if (techDevelopment > 70) { ecoChange += 0.5; hapChange += 0.2; } // High tech boosts economy and happiness
            if (relations > 70) ecoChange += 0.3; // Good relations boost economy
            if (efficiency < 50) { ecoChange -= 0.5; hapChange -= 0.2; stabChange -= 0.3; } // Low efficiency hurts all
            if (armySize > population * 1000) { // If army size is too large compared to population
                happiness -= 0.5; // People might be unhappy with large military
                economy -= 0.5; // High maintenance costs
            }


            // Apply country challenges
            countryChallenges.forEach(challenge => {
                if (challenge.id === 'corruption') {
                    effChange -= 0.5;
                    ecoChange -= 0.3;
                } else if (challenge.id === 'water_crisis') {
                    hapChange -= 0.5;
                    popChange -= 0.1;
                    ecoChange -= 0.2;
                }
                // Add more challenge effects here
            });

            // Apply ideology effects to quarterly changes
            const ideologyEffects = ideologies[currentIdeology].consequences;
            for (const stat in ideologyEffects) {
                switch (stat) {
                    case 'economy': ecoChange += ideologyEffects[stat] / 10; break; // Scale down for quarterly
                    case 'happiness': hapChange += ideologyEffects[stat] / 10; break;
                    case 'stability': stabChange += ideologyEffects[stat] / 10; break;
                    case 'relations': relChange += ideologyEffects[stat] / 10; break;
                    case 'techDevelopment': techChange += ideologyEffects[stat] / 10; break;
                    case 'diplomaticScore': diplomaticScore += ideologyEffects[stat] / 10; break;
                    case 'efficiency': effChange += ideologyEffects[stat] / 10; break;
                    // Note: treasury, gdp, debt, inflationRate are handled separately or directly by actions
                }
            }


            quarterlyChanges.economy = ecoChange;
            quarterlyChanges.happiness = hapChange;
            quarterlyChanges.stability = stabChange;
            quarterlyChanges.relations = relChange;
            quarterlyChanges.population = popChange;
            quarterlyChanges.gdp = gdpChange;
            quarterlyChanges.techDevelopment = techChange;
            quarterlyChanges.efficiency = effChange;
            quarterlyChanges.armySize = armyChange;


            // Calculate inflation rate (now per quarter)
            let inflationChange = ((Math.random() * 4) - 2) / 4; // Random change between -0.5 and +0.5
            if (economy > 70) inflationChange += 0.5 / 4;
            else if (economy < 30) inflationChange -= 0.5 / 4;
            inflationRate = Math.max(0, Math.min(10, inflationRate + inflationChange));
            quarterlyChanges.inflationRate = inflationChange;

            // Apply inflation to treasury (reduces its value)
            treasury = Math.round((treasury * (1 - (inflationRate / 100 / 4))) * 100) / 100;

            // Calculate maintenance costs (adjusted for inflation and efficiency)
            maintenanceCosts = Math.round((population * 0.1 + techDevelopment * 0.5 + armySize * 0.05) * (1 + (inflationRate / 100 * 0.5)) * (1 + (100 - efficiency) / 100 * 0.2) * 100) / 100;
            
            // Calculate tax and industry revenue (adjusted for efficiency)
            taxRevenue = Math.round((population * (economy / 100) * 0.5) * efficiency / 100 * 100) / 100;
            industryRevenue = Math.round((gdp * (economy / 100) * 0.02) * efficiency / 100 * 100) / 100;
            
            const totalIncome = taxRevenue + industryRevenue;
            annualBalance = Math.round((totalIncome - maintenanceCosts) * 100) / 100;

            // Apply quarterly balance to treasury
            treasury += annualBalance / 4;
            treasury = Math.round(treasury * 100) / 100;

            totalMoneyGenerated = Math.round((totalMoneyGenerated + totalIncome / 4) * 100) / 100;

            diplomaticScore += (Math.floor(Math.random() * 3) - 1) / 4;
            quarterlyChanges.diplomaticScore = (Math.floor(Math.random() * 3) - 1) / 4;


            // Apply the quarterly changes
            economy = Math.max(0, Math.min(100, economy + quarterlyChanges.economy));
            happiness = Math.max(0, Math.min(100, happiness + quarterlyChanges.happiness));
            stability = Math.max(0, Math.min(100, stability + quarterlyChanges.stability));
            relations = Math.max(0, Math.min(100, relations + quarterlyChanges.relations));
            techDevelopment = Math.max(0, Math.min(100, techDevelopment + quarterlyChanges.techDevelopment));
            population = Math.max(1, population + quarterlyChanges.population);
            gdp = Math.max(0, gdp + quarterlyChanges.gdp);
            diplomaticScore = Math.max(0, Math.min(100, diplomaticScore + quarterlyChanges.diplomaticScore));
            efficiency = Math.max(0, Math.min(100, efficiency + quarterlyChanges.efficiency));
            armySize = Math.max(0, armySize + quarterlyChanges.armySize); // Army size can't go below 0

            addNewsEntry(`Reporte Trimestral (Año ${year}, Mes ${currentMonth})`, `La economía, felicidad y estabilidad del país han fluctuado.`, quarterlyChanges);

            updateStats(); // Update stats after quarterly changes

            // Generate 3 new events for the current turn
            gameEventsQueue = []; // Clear previous events
            for (let i = 0; i < 3; i++) {
                const randomIndex = Math.floor(Math.random() * events.length);
                gameEventsQueue.push(events[randomIndex]);
            }

            // Process the first event of the new turn
            processNextEventInTurn();
        }

        // Function to apply consequences after a decision
        function applyDecisionConsequences(consequences) {
            const changes = {}; // To track actual changes for news log

            const efficiencyFactor = efficiency / 100; // Efficiency scales the effectiveness of positive impacts

            // Apply consequences, accounting for efficiency
            for (const stat in consequences) {
                let change = consequences[stat];
                if (stat === 'bilateralRelations') { // Handle bilateral relations separately
                    const targetCountry = getSelectedDiplomacyCountry();
                    if (targetCountry) {
                        modifyBilateralRelations(targetCountry, change);
                    }
                    continue; // Skip to next stat
                }

                // Apply efficiency to positive changes for core stats
                if (change > 0 && ['economy', 'happiness', 'stability', 'relations', 'techDevelopment', 'diplomaticScore', 'efficiency', 'armySize'].includes(stat)) {
                    change *= efficiencyFactor;
                }

                // Store current value before applying change for news log
                let oldValue;
                switch (stat) {
                    case 'economy': oldValue = economy; economy += change; animateStatChange('economyValue'); break;
                    case 'happiness': oldValue = happiness; happiness += change; animateStatChange('happinessValue'); break;
                    case 'stability': oldValue = stability; stability += change; animateStatChange('stabilityValue'); break;
                    case 'relations': oldValue = relations; relations += change; animateStatChange('relationsValue'); break;
                    case 'population': oldValue = population; population += change; animateStatChange('populationValue'); break;
                    case 'gdp': oldValue = gdp; gdp += change; animateStatChange('gdpValueModal'); break;
                    case 'techDevelopment': oldValue = techDevelopment; techDevelopment += change; animateStatChange('techDevelopmentValue'); break;
                    case 'treasury': oldValue = treasury; treasury += change; animateStatChange('treasuryValueModal'); break;
                    case 'diplomaticScore': oldValue = diplomaticScore; diplomaticScore += change; animateStatChange('diplomaticScoreValue'); break;
                    case 'debt': oldValue = debt; debt += change; animateStatChange('debtValueModal'); break;
                    case 'inflationRate': oldValue = inflationRate; inflationRate += change; animateStatChange('inflationRateValueModal'); break;
                    case 'efficiency': oldValue = efficiency; efficiency += change; animateStatChange('efficiencyValue'); break;
                    case 'armySize': oldValue = armySize; armySize += change; animateStatChange('armySizeValue'); break;
                }
                // Calculate the actual change that occurred after applying caps
                let actualChange = 0;
                switch (stat) {
                    case 'economy': actualChange = economy - oldValue; break;
                    case 'happiness': actualChange = happiness - oldValue; break;
                    case 'stability': actualChange = stability - oldValue; break;
                    case 'relations': actualChange = relations - oldValue; break;
                    case 'population': actualChange = population - oldValue; break;
                    case 'gdp': actualChange = gdp - oldValue; break;
                    case 'techDevelopment': actualChange = techDevelopment - oldValue; break;
                    case 'treasury': actualChange = treasury - oldValue; break;
                    case 'diplomaticScore': actualChange = diplomaticScore - oldValue; break;
                    case 'debt': actualChange = debt - oldValue; break;
                    case 'inflationRate': actualChange = inflationRate - oldValue; break;
                    case 'efficiency': actualChange = efficiency - oldValue; break;
                    case 'armySize': actualChange = armySize - oldValue; break;
                }
                changes[stat] = actualChange; // Record the actual change
            }

            // Ensure stats stay within valid ranges after applying effects
            economy = Math.max(0, Math.min(100, economy));
            happiness = Math.max(0, Math.min(100, happiness));
            stability = Math.max(0, Math.min(100, stability));
            relations = Math.max(0, Math.min(100, relations));
            techDevelopment = Math.max(0, Math.min(100, techDevelopment));
            population = Math.max(1, population); // Minimum population of 1M
            gdp = Math.max(0, gdp);
            diplomaticScore = Math.max(0, Math.min(100, diplomaticScore));
            inflationRate = Math.max(0, Math.min(10, inflationRate)); // Inflation cap
            efficiency = Math.max(0, Math.min(100, efficiency)); // Efficiency cap
            armySize = Math.max(0, armySize); // Army size can't go below 0

            updateStats(); // Update stats immediately after decision
            return changes; // Return the actual changes for news logging
        }

        // New function to process the next event in the current turn's queue
        function processNextEventInTurn() {
            if (gameEventsQueue.length > 0) {
                const event = gameEventsQueue.shift(); // Get the next event
                eventText.textContent = event.text;

                optionsContainer.innerHTML = ''; // Clear previous options
                event.options.forEach((option, index) => {
                    const button = document.createElement('button');
                    button.id = `option${index + 1}`;
                    button.className = 'button-option';
                    button.innerHTML = option.text;
                    // Directly apply consequences and add to news log
                    button.addEventListener('click', () => {
                        clickSound.play();
                        const actualChanges = applyDecisionConsequences(option.consequences);
                        addNewsEntry("Decisión de Evento", option.text, actualChanges);
                        processNextEventInTurn(); // Move to next event or turn
                    });
                    optionsContainer.appendChild(button);
                });
            } else {
                // All events for the current turn are processed, advance to the next turn (quarter)
                nextTurn();
            }
        }

        // Define possible country challenges
        const possibleChallenges = [
            { id: 'corruption', name: "Corrupción Endémica", description: "La corrupción es un problema arraigado, reduciendo la eficiencia gubernamental y la economía." },
            { id: 'water_crisis', name: "Crisis Hídrica", description: "La escasez de agua afecta la felicidad de la población y la producción agrícola." },
            { id: 'aging_population', name: "Población Envejecida", description: "Una población mayor ejerce presión sobre los servicios de salud y la fuerza laboral." },
            { id: 'natural_disaster_prone', name: "Zona de Desastres Naturales", description: "El país es propenso a desastres naturales, lo que aumenta los costos de mantenimiento." },
            { id: 'political_instability', name: "Inestabilidad Política Crónica", description: "Frecuentes cambios de gobierno y disturbios sociales afectan la estabilidad." },
            { id: 'resource_dependency', name: "Dependencia de Recursos", description: "La economía depende en gran medida de un solo recurso, haciéndola vulnerable a las fluctuaciones del mercado." },
            { id: 'brain_drain', name: "Fuga de Cerebros", description: "La emigración de profesionales calificados frena el desarrollo tecnológico." },
        ];

        // Function to assign random challenges based on initial stats
        function assignCountryChallenges(initialStats) {
            countryChallenges = [];
            let challengeCount = 0;

            // More challenges for countries with lower initial stats
            if (initialStats.economy < 50) challengeCount++;
            if (initialStats.happiness < 50) challengeCount++;
            if (initialStats.stability < 50) challengeCount++;
            if (initialStats.efficiency < 50) challengeCount++;

            challengeCount = Math.min(3, challengeCount); // Max 3 challenges

            const shuffledChallenges = [...possibleChallenges].sort(() => 0.5 - Math.random());
            for (let i = 0; i < challengeCount; i++) {
                countryChallenges.push(shuffledChallenges[i]);
            }
            updateCountryChallengesDisplay();
        }

        // Function to update the display of active country challenges
        function updateCountryChallengesDisplay() {
            activeChallengesList.innerHTML = '';
            if (countryChallenges.length > 0) {
                countryChallengesSection.classList.remove('hidden');
                countryChallenges.forEach(challenge => {
                    const div = document.createElement('div');
                    div.className = 'challenge-item';
                    div.innerHTML = `
                        <span class="text-red-500">🔥</span>
                        <span>${challenge.name}: ${challenge.description}</span>
                    `;
                    activeChallengesList.appendChild(div);
                });
            } else {
                countryChallengesSection.classList.add('hidden');
            }
        }


        // Función para reiniciar el juego a sus valores iniciales
        function resetGame() {
            // Obtener datos para el país seleccionado o usar el predeterminado
            const selectedCountryData = countryData[currentCountry] || countryData["Default"];

            economy = selectedCountryData.initialStats.economy;
            happiness = selectedCountryData.initialStats.happiness;
            stability = selectedCountryData.initialStats.stability;
            relations = selectedCountryData.initialStats.relations; // Global relations
            population = selectedCountryData.initialStats.population;
            gdp = selectedCountryData.initialStats.gdp;
            techDevelopment = selectedCountryData.initialStats.techDevelopment;
            treasury = selectedCountryData.initialStats.treasury;
            totalMoneyGenerated = 0; // Reset for a new game
            diplomaticScore = selectedCountryData.initialStats.diplomaticScore;
            inflationRate = 2.0; // Reset inflation for a new game
            debt = selectedCountryData.initialStats.debt; // Reset debt for a new game
            currencyValue = 1.0; // Reset currency value
            unAidCount = 0; // Reset UN aid count for a new game
            efficiency = selectedCountryData.initialStats.efficiency; // Set initial efficiency
            currentIdeology = selectedCountryData.initialStats.ideology; // Set initial ideology
            armySize = selectedCountryData.initialStats.armySize; // Set initial army size

            // Initialize bilateral relations for all countries to 0
            playerRelations = new Map();
            countriesList.forEach(country => {
                playerRelations.set(country, 0); // Start all bilateral relations at 0
            });

            // If the player's chosen country is in the countryData list, ensure its own relations are high
            if (countryData[currentCountry]) {
                playerRelations.set(currentCountry, 100); // Self-relation is 100
            }

            // Assign country-specific challenges
            assignCountryChallenges(selectedCountryData.initialStats);

            // Reiniciar variables de desglose económico
            taxRevenue = 0;
            industryRevenue = 0;
            maintenanceCosts = 0;
            annualBalance = 0;

            year = 1;
            currentMonth = 1; // Reset month for new game
            currentCountryFlagEmoji = selectedCountryData.flag; // Establecer bandera emoji

            // Clear news feed
            newsFeedContainer.innerHTML = '';
            addNewsEntry("Inicio del Juego", `¡Bienvenido a NationLife! Has tomado el control de ${currentCountry}.`, {});


            updateStats(); // Actualizar la interfaz con los valores reiniciados
            eventText.textContent = `¡Bienvenido a NationLife! Eres el nuevo líder de ${currentCountry}. Tu primera tarea es guiarlo hacia la prosperidad.`;
            optionsContainer.innerHTML = '<button id="option1" class="button-option">Comenzar <span class="ml-2">🚀</span></button>';
            document.getElementById('option1').addEventListener('click', () => {
                clickSound.play();
                // Initial setup for the first turn's events
                gameEventsQueue = [];
                for (let i = 0; i < 3; i++) { // Generate initial 3 events
                    const randomIndex = Math.floor(Math.random() * events.length);
                    gameEventsQueue.push(events[randomIndex]);
                }
                processNextEventInTurn(); // Show the first event
            });
        }

        // Función para iniciar el juego con el país seleccionado
        function startGame() {
            // Si no se selecciona ningún país, mostrar mensaje y no iniciar
            if (!currentCountry || countrySelect.value === "") {
                 showMessage("Por favor, selecciona un país.");
                 return;
            }
            // Si se selecciona un país, actualizar currentCountry
            currentCountry = countrySelect.value;
            resetGame(); // Reiniciar el juego con los datos del país seleccionado
            showScreen('gameScreen');
            // The first turn will be initiated by the "Comenzar" button on the game screen
        }

        // Definición de eventos del juego
        const events = [
            {
                text: "Una nueva enfermedad se está propagando rápidamente por el país. ¿Cómo respondes?",
                options: [
                    {
                        text: "Invertir fuertemente en investigación médica y vacunas. 🧪",
                        consequences: { economy: -20, happiness: +15, stability: +5, relations: +10, techDevelopment: +10, treasury: -50 }
                    },
                    {
                        text: "Implementar una cuarentena estricta y cerrar fronteras. 😷",
                        consequences: { economy: -10, happiness: -10, stability: +10, relations: -5, population: -5, treasury: -10 }
                    },
                    {
                        text: "Ignorar la enfermedad y dejar que la naturaleza siga su curso. 🤷‍♂️",
                        consequences: { economy: +5, happiness: -30, stability: -20, relations: -10, population: -15, treasury: +5 }
                    }
                ]
            },
            {
                text: "Se ha descubierto un gran yacimiento de recursos naturales. ¿Qué haces?",
                options: [
                    {
                        text: "Explotarlo rápidamente para obtener beneficios a corto plazo. 💰",
                        consequences: { economy: +30, happiness: -5, stability: +5, relations: -5, gdp: +100, treasury: +150 }
                    },
                    {
                        text: "Desarrollarlo de forma sostenible y con inversión extranjera. 🌳",
                        consequences: { economy: +15, happiness: +10, stability: +10, relations: +15, gdp: +50, techDevelopment: +5, treasury: +70 }
                    },
                    {
                        text: "Nacionalizar los recursos y prohibir la explotación privada. 🚫",
                        consequences: { economy: -10, happiness: +20, stability: -10, relations: -20, gdp: -20, treasury: -10 }
                    }
                ]
            },
            {
                text: "Un país vecino ha declarado la guerra a una nación aliada. ¿Cómo actúas?",
                options: [
                    {
                        text: "Enviar tropas y recursos para apoyar a tu aliado. ⚔️",
                        consequences: { economy: -25, happiness: -5, stability: -5, relations: +20, gdp: -50, treasury: -100, diplomaticScore: +15 }
                    },
                    {
                        text: "Ofrecer mediación diplomática y buscar una solución pacífica. 🕊️",
                        consequences: { economy: +5, happiness: +5, stability: +5, relations: +10, diplomaticScore: +10 }
                    },
                    {
                        text: "Mantenerte neutral y proteger tus propios intereses. 🛡️",
                        consequences: { economy: +10, happiness: -10, stability: +10, relations: -15, diplomaticScore: -10 }
                    }
                ]
            },
            {
                text: "Hay una gran ola de protestas por la desigualdad social. ¿Cómo manejas la situación?",
                options: [
                    {
                        text: "Implementar reformas sociales y económicas ambiciosas. ⚖️",
                        consequences: { economy: -15, happiness: +25, stability: +10, relations: +5, gdp: -10, treasury: -30 }
                    },
                    {
                        text: "Reprimir las protestas con fuerza policial. 🚨",
                        consequences: { economy: +5, happiness: -20, stability: +15, relations: -10, treasury: +10 }
                    },
                    {
                        text: "Ignorar las protestas y esperar que se disuelva. 🤷‍♂️",
                        consequences: { economy: 0, happiness: -30, stability: -20, relations: -5 }
                    }
                ]
            },
            {
                text: "Se acerca una elección presidencial. ¿Qué estrategia adoptas?",
                options: [
                    {
                        text: "Prometer grandes inversiones en infraestructura y empleo. 🏗️",
                        consequences: { economy: -10, happiness: +15, stability: +5, relations: 0, gdp: +20, treasury: -40 }
                    },
                    {
                        text: "Enfocarte en la estabilidad y la reducción de impuestos. 📊",
                        consequences: { economy: +10, happiness: -5, stability: +10, relations: 0, treasury: +20 }
                    },
                    {
                        text: "Campaña populista con promesas difíciles de cumplir. 🗣️",
                        consequences: { economy: -5, happiness: +20, stability: -10, relations: -0, treasury: -10 }
                    }
                ]
            },
            {
                text: "Un desastre natural (terremoto/inundación) ha golpeado una región. ¿Cómo respondes?",
                options: [
                    {
                        text: "Movilizar recursos de emergencia y ayuda humanitaria. 🆘",
                        consequences: { economy: -25, happiness: +20, stability: +10, relations: +5, gdp: -30, population: -2, treasury: -60 }
                    },
                    {
                        text: "Priorizar la reconstrucción rápida con apoyo militar. 👷‍♂️",
                        consequences: { economy: -15, happiness: +10, stability: +15, relations: 0, gdp: -20, treasury: -40 }
                    },
                    {
                        text: "Solicitar ayuda internacional y enfocar recursos en otras áreas. 🤝",
                        consequences: { economy: +5, happiness: -10, stability: -5, relations: +10, gdp: -10, treasury: -10, diplomaticScore: +5 }
                    }
                ]
            },
            {
                text: "Un grupo de hackers ha atacado la infraestructura digital del país. ¿Qué haces?",
                options: [
                    {
                        text: "Invertir en ciberseguridad y buscar a los culpables. 💻",
                        consequences: { economy: -15, happiness: +5, stability: +10, relations: +5, techDevelopment: +10, treasury: -25 }
                    },
                    {
                        text: "Lanzar un contraataque cibernético. 💥",
                        consequences: { economy: -10, happiness: -5, stability: -5, relations: -15, treasury: -15, diplomaticScore: -10 }
                    },
                    {
                        text: "Pedir ayuda a aliados internacionales para resolver el problema. 🌐",
                        consequences: { economy: -5, happiness: +5, stability: +5, relations: +10, treasury: -5, diplomaticScore: +5 }
                    }
                ]
            },
            {
                text: "Hay un debate sobre la inversión en energía renovable versus combustibles fósiles. ¿Cuál es tu postura?",
                options: [
                    {
                        text: "Priorizar la energía renovable con grandes subsidios. ☀️",
                        consequences: { economy: -10, happiness: +10, stability: +5, relations: +10, techDevelopment: +15, treasury: -30 }
                    },
                    {
                        text: "Continuar con los combustibles fósiles para mantener la economía actual. ⛽",
                        consequences: { economy: +10, happiness: -5, stability: +10, relations: -5, treasury: +20 }
                    },
                    {
                        text: "Buscar un equilibrio, invirtiendo en ambos sectores. ⚖️",
                        consequences: { economy: 0, happiness: 0, stability: 0, relations: 0, techDevelopment: +5, treasury: -5 }
                    }
                ]
            },
            {
                text: "Un movimiento separatista gana fuerza en una región. ¿Cómo respondes?",
                options: [
                    {
                        text: "Negociar autonomía y mayor descentralización. 🤝",
                        consequences: { economy: -5, happiness: +10, stability: +5, relations: +5, diplomaticScore: +5 }
                    },
                    {
                        text: "Reprimir el movimiento con fuerza militar. 🚨",
                        consequences: { economy: +5, happiness: -15, stability: +15, relations: -10, population: -1 }
                    },
                    {
                        text: "Ignorar el movimiento y esperar que se disuelva. 🤷‍♂️",
                        consequences: { economy: 0, happiness: -10, stability: -10, relations: -5 }
                    }
                ]
            },
            {
                text: "Se ha producido un avance científico importante en tu país. ¿Cómo lo utilizas?",
                options: [
                    {
                        text: "Invertir en su desarrollo para aplicaciones comerciales. 🔬",
                        consequences: { economy: +20, happiness: +5, techDevelopment: +15, treasury: -20 }
                    },
                    {
                        text: "Compartirlo con la comunidad internacional para el bien común. 🌐",
                        consequences: { economy: -5, happiness: +10, relations: +15, techDevelopment: +5, diplomaticScore: +10 }
                    },
                    {
                        text: "Mantenerlo en secreto para obtener ventaja estratégica. 🤫",
                        consequences: { economy: +10, happiness: -5, relations: -10, techDevelopment: +10, diplomaticScore: -5 }
                    }
                ]
            },
            // Nuevos eventos
            {
                text: "Se propone organizar un gran festival cultural nacional para celebrar la diversidad del país. ¿Cómo respondes?",
                options: [
                    {
                        text: "Apoyar el festival con fondos significativos. 🎉",
                        consequences: { economy: -10, happiness: +20, relations: +5, treasury: -20 }
                    },
                    {
                        text: "Permitir el festival, pero con poco apoyo estatal. 🎭",
                        consequences: { economy: 0, happiness: +10, relations: 0 }
                    },
                    {
                        text: "Prohibir el festival por motivos de seguridad. 🚫",
                        consequences: { economy: +5, happiness: -15, stability: +5 }
                    }
                ]
            },
            {
                text: "Un equipo de arqueólogos ha descubierto ruinas antiguas de gran valor histórico. ¿Qué haces?",
                options: [
                    {
                        text: "Invertir en excavación y desarrollo turístico. 🏛️",
                        consequences: { economy: +10, happiness: +10, techDevelopment: +5, treasury: -15 }
                    },
                    {
                        text: "Proteger el sitio y limitar el acceso. 🔒",
                        consequences: { economy: 0, happiness: +5, stability: +5 }
                    },
                    {
                        text: "Ignorar el descubrimiento y continuar con el desarrollo. 🚧",
                        consequences: { economy: +5, happiness: -5, techDevelopment: -2 }
                    }
                ]
            },
            {
                text: "Una gran afluencia de refugiados de un país vecino busca asilo en tu nación. ¿Cómo respondes?",
                options: [
                    {
                        text: "Abrir las fronteras y ofrecer ayuda humanitaria. 🕊️",
                        consequences: { economy: -15, happiness: +15, relations: +10, population: +1 }
                    },
                    {
                        text: "Cerrar las fronteras y reforzar la seguridad. 🧱",
                        consequences: { economy: +5, happiness: -10, stability: +10, relations: -10 }
                    },
                    {
                        text: "Establecer campos de refugiados temporales. ⛺",
                        consequences: { economy: -5, happiness: 0, stability: 0 }
                    }
                ]
            },
            {
                text: "Los sindicatos han convocado una huelga general exigiendo mejores condiciones laborales. ¿Qué haces?",
                options: [
                    {
                        text: "Negociar con los sindicatos y aceptar algunas demandas. 🤝",
                        consequences: { economy: -10, happiness: +15, stability: +5 }
                    },
                    {
                        text: "Declarar la huelga ilegal y reprimirla. 🚨",
                        consequences: { economy: +5, happiness: -20, stability: +10 }
                    },
                    {
                        text: "No intervenir y dejar que la huelga continúe. 🤷",
                        consequences: { economy: -10, happiness: -10, stability: -5 }
                    }
                ]
            },
            {
                text: "Científicos nacionales han logrado un avance significativo en inteligencia artificial. ¿Cómo lo utilizas?",
                options: [
                    {
                        text: "Invertir masivamente en IA y automatización. 🤖",
                        consequences: { techDevelopment: +20, economy: +15, happiness: -5, population: -0.5, treasury: -30 }
                    },
                    {
                        text: "Regular estrictamente la IA para evitar riesgos. ⚖️",
                        consequences: { techDevelopment: +5, happiness: +10, stability: +10 }
                    },
                    {
                        text: "Exportar la tecnología para obtener beneficios rápidos. 💸",
                        consequences: { economy: +20, relations: +10, techDevelopment: 0, treasury: +50 }
                    }
                ]
            }
        ];

        // Initialize the game
        document.addEventListener('DOMContentLoaded', () => {
            // Show main menu screen on load
            showScreen('mainMenuScreen');

            // Set initial music volume from slider value on load
            backgroundMusic.volume = musicVolumeSlider.value; // Ensure initial sync

            // Assign events to main menu buttons
            btnChooseCountry.addEventListener('click', () => {
                clickSound.play();
                showScreen('countrySelectionScreen');
            });
            btnContinueGame.addEventListener('click', () => {
                clickSound.play();
                // For now, "Continue Game" also starts a new game with the default country
                // In the future, saved game loading logic would be implemented here.
                currentCountry = "Nación Unida"; // Ensure a country is selected
                resetGame();
                showScreen('gameScreen');
            });

            // Assign events to country selection screen buttons
            btnConfirmCountry.addEventListener('click', () => {
                clickSound.play();
                const selectedCountry = countrySelect.value;
                if (selectedCountry) {
                    currentCountry = selectedCountry;
                    startGame();
                } else {
                    showMessage("Por favor, selecciona un país.");
                }
            });
            btnBackToMain.addEventListener('click', () => {
                clickSound.play();
                showScreen('mainMenuScreen');
            });

            // Event for the search input
            countrySearchInput.addEventListener('input', (event) => {
                populateCountrySelect(event.target.value);
            });

            // Event listeners for the new settings modal
            btnOpenSettingsModal.addEventListener('click', () => { clickSound.play(); openSettingsModal(); });
            btnCloseSettingsModal.addEventListener('click', () => { clickSound.play(); closeSettingsModal(); });
            settingsModal.addEventListener('click', (event) => {
                if (event.target === settingsModal) { // Close if clicked on backdrop
                    clickSound.play();
                    closeSettingsModal();
                }
            });

            // Event listeners for the economic modal (moved back to footer)
            btnOpenEconomicModal.addEventListener('click', () => { clickSound.play(); openEconomicModal(); });
            btnCloseEconomicModal.addEventListener('click', () => { clickSound.play(); closeEconomicModal(); });
            economicModal.addEventListener('click', (event) => {
                if (event.target === economicModal) { // Close if clicked on backdrop
                    clickSound.play();
                    closeEconomicModal();
                }
            });

            // Event listeners for the new general stats modal (moved back to footer)
            btnOpenGeneralStatsModal.addEventListener('click', () => { clickSound.play(); openGeneralStatsModal(); });
            btnCloseGeneralStatsModal.addEventListener('click', () => { clickSound.play(); closeGeneralStatsModal(); });
            generalStatsModal.addEventListener('click', (event) => {
                if (event.target === generalStatsModal) { // Close if clicked on backdrop
                    clickSound.play();
                    closeGeneralStatsModal();
                }
            });

            // Event listeners for the new relations modal (moved back to footer)
            btnOpenRelationsModal.addEventListener('click', () => { clickSound.play(); openRelationsModal(); });
            btnCloseRelationsModal.addEventListener('click', () => { clickSound.play(); closeRelationsModal(); });
            relationsModal.addEventListener('click', (event) => {
                if (event.target === relationsModal) { // Close if clicked on backdrop
                    clickSound.play();
                    closeRelationsModal();
                }
            });

            // Event listener for diplomacy country selection change
            diplomacyCountrySelect.addEventListener('change', updateBilateralRelationsDisplay);

            // Event listeners for diplomatic action buttons
            btnBreakRelations.addEventListener('click', () => { clickSound.play(); breakRelations(); });
            btnIncreaseRelations.addEventListener('click', () => { clickSound.play(); increaseRelations(); });
            btnTradeAgreement.addEventListener('click', () => { clickSound.play(); makeTradeAgreement(); });
            // btnScientificAgreement.addEventListener('click', () => { clickSound.play(); makeScientificAgreement(); }); // Removed
            btnDeclareWar.addEventListener('click', () => { clickSound.play(); declareWar(); });
            btnSendGift.addEventListener('click', () => { clickSound.play(); sendGift(); });
            btnFormAlliance.addEventListener('click', () => { clickSound.play(); formAlliance(); });
            // btnCulturalExchange.addEventListener('click', () => { clickSound.play(); culturalExchange(); }); // Removed
            // btnEspionage.addEventListener('click', () => { clickSound.play(); espionage(); }); // Removed
            // btnHumanitarianAid.addEventListener('click', () => { clickSound.play(); humanitarianAid(); }); // Removed

            // Event listeners for Fund Allocation Modal (moved back to footer)
            btnOpenFundAllocationModal.addEventListener('click', () => { clickSound.play(); openFundAllocationModal(); });
            btnCloseFundAllocationModal.addEventListener('click', () => { clickSound.play(); closeFundAllocationModal(); });
            fundAllocationModal.addEventListener('click', (event) => {
                if (event.target === fundAllocationModal) {
                    clickSound.play();
                    closeFundAllocationModal();
                }
            });
            btnConfirmFundAllocation.addEventListener('click', () => { clickSound.play(); confirmFundAllocation(); });

            // Event listeners for new economic actions (now call confirmation handlers)
            btnPayDebt.addEventListener('click', () => { clickSound.play(); handlePayDebt(); });
            btnPrintMoney.addEventListener('click', () => { clickSound.play(); handlePrintMoney(); });
            btnTakeLoan.addEventListener('click', () => { clickSound.play(); handleTakeLoan(); });

            // Event listeners for Ideology Modal
            btnOpenIdeologyModal.addEventListener('click', () => { clickSound.play(); openIdeologyModal(); });
            btnCloseIdeologyModal.addEventListener('click', () => { clickSound.play(); closeIdeologyModal(); });
            ideologyModal.addEventListener('click', (event) => {
                if (event.target === ideologyModal) {
                    clickSound.play();
                    closeIdeologyModal();
                }
            });
            btnApplyIdeology.addEventListener('click', () => { clickSound.play(); applyIdeologyChange(); });

            // Event listeners for Military Modal
            btnOpenMilitaryModal.addEventListener('click', () => { clickSound.play(); openMilitaryModal(); });
            btnCloseMilitaryModal.addEventListener('click', () => { clickSound.play(); closeMilitaryModal(); });
            militaryModal.addEventListener('click', (event) => {
                if (event.target === militaryModal) {
                    clickSound.play();
                    closeMilitaryModal();
                }
            });
            btnTrainArmy.addEventListener('click', () => { clickSound.play(); handleTrainArmy(); });
            btnHireMercenaries.addEventListener('click', () => { clickSound.play(); handleHireMercenaries(); });
            btnRecruitVolunteers.addEventListener('click', () => { clickSound.play(); handleRecruitVolunteers(); });


            // Event listeners for the confirmation modal buttons
            btnConfirmAction.addEventListener('click', () => {
                clickSound.play();
                if (onConfirmCallback) {
                    onConfirmCallback();
                }
                hideConfirmationModal();
            });
            btnCancelAction.addEventListener('click', () => { clickSound.play(); hideConfirmationModal(); });
            confirmationModal.addEventListener('click', (event) => {
                if (event.target === confirmationModal) { // Close if clicked on backdrop
                    clickSound.play();
                    hideConfirmationModal();
                }
            });

            // Event listener for the close message button
            closeMessageButton.addEventListener('click', () => { clickSound.play(); hideMessage(); });

            // Audio controls event listeners
            playPauseMusicBtn.addEventListener('click', () => {
                clickSound.play();
                if (backgroundMusic.paused) {
                    backgroundMusic.play();
                    playPauseMusicBtn.textContent = '⏸️'; // Pause emoji
                } else {
                    backgroundMusic.pause();
                    playPauseMusicBtn.textContent = '🎵'; // Music note emoji
                }
            });

            musicVolumeSlider.addEventListener('input', (event) => {
                backgroundMusic.volume = event.target.value;
                console.log('Music volume set to:', backgroundMusic.volume); // Debugging line
            });
        });
    </script>
</body>
</html>
�
