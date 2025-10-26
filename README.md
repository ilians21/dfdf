<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Generador de Informes de Edificios</title>
    <script src="https://unpkg.com/docx@7.1.0/build/index.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/FileSaver.js/2.0.5/FileSaver.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1400px;
            margin: 0 auto;
            background: white;
            border-radius: 15px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            overflow: hidden;
        }

        .header {
            background: linear-gradient(135deg, #2c3e50, #34495e);
            color: white;
            padding: 30px;
            text-align: center;
        }

        .header h1 {
            font-size: 2.5em;
            margin-bottom: 10px;
        }

        .header p {
            font-size: 1.1em;
            opacity: 0.9;
        }

        .main-content {
            display: grid;
            grid-template-columns: 350px 1fr;
            gap: 0;
            min-height: 800px;
        }

        .sidebar {
            background: #f8f9fa;
            padding: 25px;
            border-right: 1px solid #e9ecef;
        }

        .content {
            padding: 25px;
            background: white;
        }

        .section-title {
            font-size: 1.3em;
            font-weight: 600;
            margin-bottom: 15px;
            color: #2c3e50;
            border-bottom: 2px solid #3498db;
            padding-bottom: 8px;
        }

        .form-group {
            margin-bottom: 20px;
        }

        .form-group label {
            display: block;
            margin-bottom: 8px;
            font-weight: 500;
            color: #495057;
        }

        .form-control {
            width: 100%;
            padding: 12px;
            border: 2px solid #e9ecef;
            border-radius: 8px;
            font-size: 14px;
            transition: border-color 0.3s;
        }

        .form-control:focus {
            outline: none;
            border-color: #3498db;
        }

        textarea.form-control {
            resize: vertical;
            min-height: 120px;
            font-family: inherit;
        }

        .btn {
            padding: 12px 24px;
            border: none;
            border-radius: 8px;
            font-size: 14px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
            text-align: center;
            display: inline-block;
            width: 100%;
            margin-bottom: 10px;
        }

        .btn-primary {
            background: linear-gradient(135deg, #3498db, #2980b9);
            color: white;
        }

        .btn-primary:hover {
            background: linear-gradient(135deg, #2980b9, #2573a7);
            transform: translateY(-2px);
        }

        .btn-success {
            background: linear-gradient(135deg, #27ae60, #219a52);
            color: white;
        }

        .btn-success:hover {
            background: linear-gradient(135deg, #219a52, #1e8b4a);
            transform: translateY(-2px);
        }

        .btn-info {
            background: linear-gradient(135deg, #17a2b8, #138496);
            color: white;
        }

        .btn-info:hover {
            background: linear-gradient(135deg, #138496, #117a8b);
            transform: translateY(-2px);
        }

        .images-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
            gap: 15px;
            margin-bottom: 25px;
            max-height: 400px;
            overflow-y: auto;
            padding: 10px;
            border: 2px dashed #dee2e6;
            border-radius: 10px;
        }

        .image-item {
            position: relative;
            border-radius: 10px;
            overflow: hidden;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
            transition: transform 0.3s;
        }

        .image-item:hover {
            transform: scale(1.05);
        }

        .image-item img {
            width: 100%;
            height: 150px;
            object-fit: cover;
            display: block;
        }

        .image-info {
            padding: 10px;
            background: white;
            font-size: 12px;
        }

        .image-name {
            font-weight: 600;
            margin-bottom: 5px;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        .image-actions {
            display: flex;
            gap: 5px;
            margin-top: 5px;
        }

        .btn-sm {
            padding: 4px 8px;
            font-size: 11px;
            flex: 1;
        }

        .btn-danger {
            background: #e74c3c;
            color: white;
        }

        .btn-danger:hover {
            background: #c0392b;
        }

        .preview-section {
            margin-top: 25px;
            padding: 20px;
            background: #f8f9fa;
            border-radius: 10px;
        }

        .preview-image {
            max-width: 100%;
            max-height: 300px;
            border-radius: 10px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
            display: block;
            margin: 0 auto;
        }

        .counter {
            text-align: center;
            padding: 10px;
            background: #34495e;
            color: white;
            border-radius: 8px;
            margin-bottom: 15px;
            font-weight: 600;
        }

        .file-input {
            display: none;
        }

        .file-label {
            display: block;
            padding: 15px;
            background: #ecf0f1;
            border: 2px dashed #bdc3c7;
            border-radius: 8px;
            text-align: center;
            cursor: pointer;
            transition: all 0.3s;
            margin-bottom: 15px;
        }

        .file-label:hover {
            background: #d5dbdb;
            border-color: #95a5a6;
        }

        .last-file {
            background: #e8f4fd;
            padding: 15px;
            border-radius: 8px;
            border-left: 4px solid #3498db;
            margin-top: 15px;
            font-size: 14px;
        }

        .observations-list {
            max-height: 200px;
            overflow-y: auto;
            margin-top: 15px;
            padding: 10px;
            background: white;
            border-radius: 8px;
            border: 1px solid #e9ecef;
        }

        .observation-item {
            padding: 8px;
            border-bottom: 1px solid #f1f1f1;
            font-size: 12px;
        }

        .observation-item:last-child {
            border-bottom: none;
        }

        .status-indicator {
            display: inline-block;
            width: 10px;
            height: 10px;
            border-radius: 50%;
            margin-right: 5px;
        }

        .status-observed {
            background: #27ae60;
        }

        .status-pending {
            background: #f39c12;
        }

        .loading {
            display: none;
            text-align: center;
            padding: 20px;
        }

        .spinner {
            border: 4px solid #f3f3f3;
            border-top: 4px solid #3498db;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 2s linear infinite;
            margin: 0 auto 15px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        @media (max-width: 1024px) {
            .main-content {
                grid-template-columns: 1fr;
            }
            
            .sidebar {
                border-right: none;
                border-bottom: 1px solid #e9ecef;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🏢 Generador de Informes de Edificios</h1>
            <p>Carga imágenes, agrega observaciones y genera informes profesionales en Word</p>
        </div>

        <div class="main-content">
            <!-- Sidebar de controles -->
            <div class="sidebar">
                <div class="section-title">📋 Controles</div>
                
                <div class="form-group">
                    <label for="imageUpload">📷 Cargar Imágenes (Máx. 100)</label>
                    <input type="file" id="imageUpload" class="file-input" multiple accept="image/*">
                    <label for="imageUpload" class="file-label">
                        🗂️ Haz clic para seleccionar imágenes<br>
                        <small>Formatos: JPG, PNG, GIF, BMP</small>
                    </label>
                </div>

                <div class="counter" id="imageCounter">
                    Imágenes cargadas: <span id="imageCount">0</span>/100
                </div>

                <button class="btn btn-danger" onclick="clearAllImages()">
                    🗑️ Eliminar Todas las Imágenes
                </button>

                <div class="form-group" style="margin-top: 25px;">
                    <div class="section-title">📝 Observación para Imagen Seleccionada</div>
                    <textarea id="observationText" class="form-control" 
                              placeholder="Describe lo observado en esta imagen específica del edificio..."></textarea>
                    <button class="btn btn-info" onclick="saveObservation()" style="margin-top: 10px;">
                        💾 Guardar Observación
                    </button>
                </div>

                <div class="form-group">
                    <div class="section-title">📊 Datos del Informe</div>
                    
                    <label for="projectName">🏗️ Nombre del Proyecto</label>
                    <input type="text" id="projectName" class="form-control" placeholder="Ej: Edificio Torres Norte">
                    
                    <label for="location">📍 Ubicación</label>
                    <input type="text" id="location" class="form-control" placeholder="Ej: Av. Principal #123">
                    
                    <label for="inspector">👤 Inspector</label>
                    <input type="text" id="inspector" class="form-control" placeholder="Tu nombre">
                </div>

                <button class="btn btn-success" onclick="generateReport()" id="generateBtn">
                    📄 Generar Informe Word
                </button>

                <div class="loading" id="loading">
                    <div class="spinner"></div>
                    <p>Generando informe Word...</p>
                </div>

                <div class="last-file" id="lastFileInfo">
                    Último informe: <span id="lastFileName">Ninguno generado</span>
                </div>

                <div class="form-group" style="margin-top: 20px;">
                    <div class="section-title">📊 Resumen</div>
                    <div id="summaryInfo">
                        <small>
                            <span class="status-indicator status-pending"></span>Imágenes sin observación: <span id="pendingCount">0</span><br>
                            <span class="status-indicator status-observed"></span>Imágenes con observación: <span id="observedCount">0</span>
                        </small>
                    </div>
                </div>
            </div>

            <!-- Contenido principal -->
            <div class="content">
                <div class="section-title">🖼️ Imágenes Cargadas 
                    <small style="font-size: 0.8em; color: #666; margin-left: 10px;">
                        <span class="status-indicator status-observed"></span>Con observación 
                        <span class="status-indicator status-pending"></span>Sin observación
                    </small>
                </div>
                
                <div class="images-grid" id="imagesGrid">
                    <div style="text-align: center; padding: 40px; color: #6c757d;">
                        <div style="font-size: 48px; margin-bottom: 10px;">📷</div>
                        <p>No hay imágenes cargadas</p>
                        <small>Selecciona imágenes usando el botón en el panel izquierdo</small>
                    </div>
                </div>

                <div class="preview-section">
                    <div class="section-title">👀 Vista Previa e Información</div>
                    <div id="imagePreview" style="text-align: center;">
                        <div style="padding: 40px; color: #6c757d;">
                            <div style="font-size: 48px; margin-bottom: 10px;">🔍</div>
                            <p>Selecciona una imagen para ver la previsualización y agregar observaciones</p>
                        </div>
                    </div>
                </div>

                <div class="observations-list">
                    <div class="section-title">📋 Todas las Observaciones Guardadas</div>
                    <div id="observationsList">
                        <div style="text-align: center; padding: 20px; color: #6c757d;">
                            No hay observaciones guardadas
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Estado de la aplicación
        const state = {
            images: [],
            currentImageIndex: -1,
            lastGeneratedFile: null
        };

        // Elementos del DOM
        const elements = {
            imageUpload: document.getElementById('imageUpload'),
            imagesGrid: document.getElementById('imagesGrid'),
            imagePreview: document.getElementById('imagePreview'),
            imageCount: document.getElementById('imageCount'),
            observationText: document.getElementById('observationText'),
            observationsList: document.getElementById('observationsList'),
            lastFileName: document.getElementById('lastFileName'),
            pendingCount: document.getElementById('pendingCount'),
            observedCount: document.getElementById('observedCount'),
            generateBtn: document.getElementById('generateBtn'),
            loading: document.getElementById('loading')
        };

        // Inicialización
        document.addEventListener('DOMContentLoaded', function() {
            elements.imageUpload.addEventListener('change', handleImageUpload);
            loadState();
        });

        // Manejar carga de imágenes
        function handleImageUpload(event) {
            const files = event.target.files;
            
            for (let file of files) {
                if (state.images.length >= 100) {
                    alert('¡Límite alcanzado! Solo se pueden cargar hasta 100 imágenes.');
                    break;
                }

                if (!file.type.startsWith('image/')) continue;

                const reader = new FileReader();
                reader.onload = function(e) {
                    const imageInfo = {
                        id: Date.now() + Math.random(),
                        name: file.name,
                        dataUrl: e.target.result,
                        observation: '',
                        uploadDate: new Date().toLocaleString('es-ES'),
                        file: file
                    };
                    
                    state.images.push(imageInfo);
                    updateUI();
                    saveState();
                };
                reader.readAsDataURL(file);
            }
            
            event.target.value = '';
        }

        // Seleccionar imagen para previsualización
        function selectImage(index) {
            state.currentImageIndex = index;
            const image = state.images[index];
            
            elements.imagePreview.innerHTML = `
                <img src="${image.dataUrl}" alt="${image.name}" class="preview-image">
                <div style="margin-top: 15px; text-align: center;">
                    <strong>${image.name}</strong><br>
                    <small>Cargada: ${image.uploadDate}</small>
                    ${image.observation ? `<div style="margin-top: 10px; padding: 10px; background: #e8f4fd; border-radius: 5px;">
                        <strong>Observación actual:</strong><br>${image.observation}
                    </div>` : ''}
                </div>
            `;
            
            elements.observationText.value = image.observation || '';
        }

        // Guardar observación
        function saveObservation() {
            if (state.currentImageIndex === -1) {
                alert('Por favor, selecciona una imagen primero.');
                return;
            }
            
            const observation = elements.observationText.value.trim();
            if (!observation) {
                alert('Por favor, escribe una observación antes de guardar.');
                return;
            }
            
            state.images[state.currentImageIndex].observation = observation;
            
            updateUI();
            saveState();
            
            showNotification('✅ Observación guardada correctamente.');
        }

        // Eliminar imagen
        function deleteImage(index, event) {
            event.stopPropagation();
            state.images.splice(index, 1);
            state.currentImageIndex = -1;
            updateUI();
            saveState();
        }

        // Eliminar todas las imágenes
        function clearAllImages() {
            if (!state.images.length) return;
            
            if (confirm('¿Estás seguro de que quieres eliminar todas las imágenes?')) {
                state.images = [];
                state.currentImageIndex = -1;
                updateUI();
                saveState();
            }
        }

        // Actualizar interfaz de usuario
        function updateUI() {
            elements.imageCount.textContent = state.images.length;
            
            const observedCount = state.images.filter(img => img.observation).length;
            const pendingCount = state.images.length - observedCount;
            elements.observedCount.textContent = observedCount;
            elements.pendingCount.textContent = pendingCount;
            
            if (state.images.length === 0) {
                elements.imagesGrid.innerHTML = `
                    <div style="text-align: center; padding: 40px; color: #6c757d;">
                        <div style="font-size: 48px; margin-bottom: 10px;">📷</div>
                        <p>No hay imágenes cargadas</p>
                        <small>Selecciona imágenes usando el botón en el panel izquierdo</small>
                    </div>
                `;
                elements.imagePreview.innerHTML = `
                    <div style="padding: 40px; color: #6c757d;">
                        <div style="font-size: 48px; margin-bottom: 10px;">🔍</div>
                        <p>Selecciona una imagen para ver la previsualización</p>
                    </div>
                `;
            } else {
                elements.imagesGrid.innerHTML = state.images.map((image, index) => `
                    <div class="image-item" onclick="selectImage(${index})" 
                         style="border: 2px solid ${image.observation ? '#27ae60' : '#f39c12'};">
                        <img src="${image.dataUrl}" alt="${image.name}">
                        <div class="image-info">
                            <div class="image-name" title="${image.name}">${image.name}</div>
                            <div class="image-actions">
                                <button class="btn btn-danger btn-sm" onclick="deleteImage(${index}, event)">
                                    🗑️
                                </button>
                                <span style="color: ${image.observation ? '#27ae60' : '#f39c12'}; font-size: 10px;">
                                    ${image.observation ? '📝' : '⏳'}
                                </span>
                            </div>
                        </div>
                    </div>
                `).join('');
            }
            
            updateObservationsList();
        }

        // Actualizar lista de observaciones
        function updateObservationsList() {
            const observations = state.images.filter(img => img.observation);
            
            if (observations.length === 0) {
                elements.observationsList.innerHTML = `
                    <div style="text-align: center; padding: 20px; color: #6c757d;">
                        No hay observaciones guardadas
                    </div>
                `;
            } else {
                elements.observationsList.innerHTML = observations.map((img, index) => `
                    <div class="observation-item">
                        <strong>${img.name}</strong><br>
                        <em>${img.uploadDate}</em><br>
                        ${img.observation}
                    </div>
                `).join('');
            }
        }

        // Convertir DataURL a Blob
        function dataURLtoBlob(dataURL) {
            const arr = dataURL.split(',');
            const mime = arr[0].match(/:(.*?);/)[1];
            const bstr = atob(arr[1]);
            let n = bstr.length;
            const u8arr = new Uint8Array(n);
            while (n--) {
                u8arr[n] = bstr.charCodeAt(n);
            }
            return new Blob([u8arr], { type: mime });
        }

        // Generar informe en Word REAL
        async function generateReport() {
            if (state.images.length === 0) {
                alert('Por favor, carga al menos una imagen antes de generar el informe.');
                return;
            }

            // Mostrar loading
            elements.generateBtn.style.display = 'none';
            elements.loading.style.display = 'block';

            try {
                const projectName = document.getElementById('projectName').value || 'Proyecto Sin Nombre';
                const location = document.getElementById('location').value || 'Ubicación No Especificada';
                const inspector = document.getElementById('inspector').value || 'Inspector No Especificado';
                const currentDate = new Date().toLocaleDateString('es-ES');

                // Crear documento Word usando docx.js
                const { Document, Paragraph, TextRun, HeadingLevel, AlignmentType, Packer } = docx;

                const doc = new Document({
                    sections: [{
                        properties: {},
                        children: [
                            // Título principal
                            new Paragraph({
                                text: "INFORME DE INSPECCIÓN DE EDIFICIOS",
                                heading: HeadingLevel.TITLE,
                                alignment: AlignmentType.CENTER,
                                spacing: { after: 400 }
                            }),

                            // Información del proyecto
                            new Paragraph({
                                text: `Proyecto: ${projectName}`,
                                spacing: { after: 100 }
                            }),
                            new Paragraph({
                                text: `Ubicación: ${location}`,
                                spacing: { after: 100 }
                            }),
                            new Paragraph({
                                text: `Inspector: ${inspector}`,
                                spacing: { after: 100 }
                            }),
                            new Paragraph({
                                text: `Fecha: ${currentDate}`,
                                spacing: { after: 400 }
                            }),

                            // Resumen ejecutivo
                            new Paragraph({
                                text: "RESUMEN EJECUTIVO",
                                heading: HeadingLevel.HEADING_1,
                                spacing: { after: 200 }
                            }),
                            new Paragraph({
                                children: [
                                    new TextRun({
                                        text: `Se realizó una inspección visual del edificio documentando ${state.images.length} observaciones mediante fotografías. ${state.images.filter(img => img.observation).length} imágenes cuentan con observaciones detalladas del estado actual de la estructura.`
                                    })
                                ],
                                spacing: { after: 400 }
                            }),

                            // Hallazgos detallados
                            new Paragraph({
                                text: "HALLAZGOS DETALLADOS",
                                heading: HeadingLevel.HEADING_1,
                                spacing: { after: 200 }
                            }),

                            // Agregar cada observación
                            ...state.images.flatMap((img, index) => [
                                new Paragraph({
                                    text: `Observación ${index + 1} de ${state.images.length}: ${img.name}`,
                                    heading: HeadingLevel.HEADING_2,
                                    spacing: { after: 100 }
                                }),
                                new Paragraph({
                                    text: `Fecha de captura: ${img.uploadDate}`,
                                    spacing: { after: 100 }
                                }),
                                new Paragraph({
                                    text: "Observación registrada:",
                                    spacing: { after: 100 }
                                }),
                                new Paragraph({
                                    text: img.observation || 'No se registró observación para esta imagen.',
                                    spacing: { after: 200 }
                                }),
                                new Paragraph({
                                    text: "―".repeat(50),
                                    alignment: AlignmentType.CENTER,
                                    spacing: { after: 200 }
                                })
                            ]),

                            // Conclusiones
                            new Paragraph({
                                text: "CONCLUSIONES Y RECOMENDACIONES",
                                heading: HeadingLevel.HEADING_1,
                                spacing: { after: 200 }
                            }),
                            new Paragraph({
                                children: [
                                    new TextRun({
                                        text: "Basado en la inspección visual documentada, se recomienda:\n\n"
                                    }),
                                    new TextRun({
                                        text: "1. Realizar mantenimiento preventivo en las áreas identificadas\n",
                                        break: 1
                                    }),
                                    new TextRun({
                                        text: "2. Programar seguimiento para verificar el estado de las observaciones\n",
                                        break: 1
                                    }),
                                    new TextRun({
                                        text: "3. Considerar reparaciones en elementos con deterioro significativo\n",
                                        break: 1
                                    }),
                                    new TextRun({
                                        text: "4. Documentar los avances en las mejoras identificadas\n\n",
                                        break: 1
                                    }),
                                    new TextRun({
                                        text: `Estadísticas del informe:\n- Total de imágenes documentadas: ${state.images.length}\n- Imágenes con observaciones: ${state.images.filter(img => img.observation).length}\n- Imágenes pendientes de observación: ${state.images.filter(img => !img.observation).length}`
                                    })
                                ],
                                spacing: { after: 200 }
                            }),

                            // Pie de página
                            new Paragraph({
                                text: "―".repeat(80),
                                alignment: AlignmentType.CENTER,
                                spacing: { after: 100 }
                            }),
                            new Paragraph({
                                text: `Este informe fue generado automáticamente por el Sistema de Inspección de Edificios - ${new Date().toLocaleString('es-ES')}`,
                                alignment: AlignmentType.CENTER,
                                size: 20
                            })
                        ]
                    }]
                });

                // Generar el documento
                const blob = await Packer.toBlob(doc);
                
                // Descargar el archivo
                const fileName = `Informe_${projectName.replace(/[^\w\s]/gi, '').replace(/\s+/g, '_')}_${new Date().getTime()}.docx`;
                saveAs(blob, fileName);
                
                // Actualizar estado
                state.lastGeneratedFile = fileName;
                elements.lastFileName.textContent = fileName;
                saveState();
                
                showNotification(`✅ Informe "${fileName}" generado correctamente!`);
                
            } catch (error) {
                console.error('Error generando el informe:', error);
                alert('Error al generar el informe. Por favor, intenta nuevamente.');
            } finally {
                // Ocultar loading
                elements.generateBtn.style.display = 'block';
                elements.loading.style.display = 'none';
            }
        }

        // Mostrar notificación
        function showNotification(message) {
            const notification = document.createElement('div');
            notification.style.cssText = `
                position: fixed;
                top: 20px;
                right: 20px;
                background: #27ae60;
                color: white;
                padding: 15px 20px;
                border-radius: 8px;
                box-shadow: 0 4px 12px rgba(0,0,0,0.15);
                z-index: 1000;
                font-weight: 500;
            `;
            notification.textContent = message;
            document.body.appendChild(notification);
            
            setTimeout(() => {
                document.body.removeChild(notification);
            }, 3000);
        }

        // Guardar estado en localStorage
        function saveState() {
            const stateToSave = {
                images: state.images.map(img => ({
                    ...img,
                    file: undefined
                })),
                lastGeneratedFile: state.lastGeneratedFile
            };
            localStorage.setItem('buildingReportState', JSON.stringify(stateToSave));
        }

        // Cargar estado desde localStorage
        function loadState() {
            const savedState = localStorage.getItem('buildingReportState');
            if (savedState) {
                const parsedState = JSON.parse(savedState);
                state.images = parsedState.images || [];
                state.lastGeneratedFile = parsedState.lastGeneratedFile;
                
                if (state.lastGeneratedFile) {
                    elements.lastFileName.textContent = state.lastGeneratedFile;
                }
                
                updateUI();
            }
        }
    </script>
</body>
</html>
