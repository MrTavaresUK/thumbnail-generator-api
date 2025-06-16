# Atualizando os arquivos para versão final com backend apontado corretamente
updated_frontend_html = """
<!DOCTYPE html>
<html lang="pt">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Gerador de Thumbnail</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet"/>
    <style>
        body { font-family: 'Inter', sans-serif; background-color: #f0f2f5; display: flex; justify-content: center; align-items: center; min-height: 100vh; margin: 0; padding: 20px; box-sizing: border-box; }
        .container { background-color: #fff; border-radius: 16px; box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1); padding: 30px; max-width: 600px; width: 100%; text-align: center; display: flex; flex-direction: column; gap: 20px; align-items: center; }
        h1 { font-size: 1.8rem; font-weight: 700; color: #2c3e50; margin-bottom: 15px; }
        textarea { width: 100%; padding: 12px; border: 1px solid #dcdcdc; border-radius: 8px; font-size: 1rem; resize: vertical; min-height: 100px; box-sizing: border-box; }
        button { background-color: #3498db; color: white; padding: 12px 25px; border: none; border-radius: 8px; font-size: 1.1rem; font-weight: 600; cursor: pointer; transition: background-color 0.3s ease, transform 0.2s ease; box-shadow: 0 4px 10px rgba(52, 152, 219, 0.3); display: flex; align-items: center; justify-content: center; gap: 8px; }
        button:hover { background-color: #2980b9; transform: translateY(-2px); }
        button:active { transform: translateY(0); }
        .image-container { width: 100%; max-width: 500px; margin-top: 20px; min-height: 300px; background-color: #e0e0e0; border-radius: 12px; display: flex; justify-content: center; align-items: center; color: #7f8c8d; font-style: italic; position: relative; overflow: hidden; box-shadow: inset 0 0 10px rgba(0, 0, 0, 0.05); }
        .image-container img { width: 100%; height: auto; border-radius: 12px; display: block; object-fit: contain; }
        .loading-spinner { border: 5px solid #f3f3f3; border-top: 5px solid #3498db; border-radius: 50%; width: 50px; height: 50px; animation: spin 1s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        .message-box { background-color: #f8d7da; color: #721c24; padding: 10px 15px; border: 1px solid #f5c6cb; border-radius: 8px; margin-top: 15px; width: 100%; box-sizing: border-box; text-align: left; word-wrap: break-word; display: none; }
    </style>
</head>
<body>
    <div class="container">
        <h1>Gerador de Thumbnail</h1>
        <textarea id="promptInput" placeholder="Descreva a imagem que você deseja gerar...">A split image for a thumbnail. On the left, a younger, disheveled person on the street being offered a helping hand by an older, kind-looking person. On the right, 30 years later, the now well-dressed, successful former homeless person, with a warm smile, extending a significant gift (like a key to a new home or a large cheque) to the same elderly person, who looks surprised and touched. The background subtly conveys the passage of time and change – a street scene on the left, a modern, pleasant setting on the right. Use warm, hopeful lighting, emphasizing gratitude and success. Focus on clear facial expressions and hands. High contrast, vivid colors. Style: realistic, inspiring.</textarea>
        <button id="generateButton">
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none"><path d="M19.34 9.16L12.59 2.41C12.2 2.02 11.56 2.02 11.17 2.41L4.42 9.16C4.03 9.55 4.03 10.18 4.42 10.57C4.6 10.75 4.86 10.84 5.12 10.84C5.38 10.84 5.64 10.75 5.82 10.57L11.09 5.29V20.25C11.09 20.76 11.51 21.18 12.02 21.18C12.53 21.18 12.95 20.76 12.95 20.25V5.29L18.22 10.57C18.61 10.96 19.24 10.96 19.63 10.57C20.02 10.18 20.02 9.55 19.63 9.16L19.34 9.16Z" fill="white"/></svg>
            Gerar Thumbnail
        </button>
        <div id="imageContainer" class="image-container">A imagem gerada aparecerá aqui.</div>
        <div id="messageBox" class="message-box"></div>
    </div>
    <script>
        const promptInput = document.getElementById('promptInput');
        const generateButton = document.getElementById('generateButton');
        const imageContainer = document.getElementById('imageContainer');
        const messageBox = document.getElementById('messageBox');

        generateButton.addEventListener('click', async () => {
            const prompt = promptInput.value;
            if (!prompt) return showMessage('Por favor, insira uma descrição.', 'error');

            imageContainer.innerHTML = '<div class="loading-spinner"></div>';
            imageContainer.style.color = 'transparent';
            showMessage('', '');

            try {
                const response = await fetch('https://thumbnail-api.onrender.com/generate', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ prompt })
                });
                const result = await response.json();

                if (result.imageUrl) {
                    imageContainer.innerHTML = `<img src="${result.imageUrl}" alt="Generated Thumbnail">`;
                } else {
                    showMessage('Erro: resposta sem imagem.', 'error');
                    imageContainer.innerHTML = 'Falha ao gerar imagem.';
                }
            } catch (err) {
                showMessage('Erro ao gerar imagem: ' + err.message, 'error');
                imageContainer.innerHTML = 'Erro de conexão.';
            }
        });

        function showMessage(message, type) {
            messageBox.textContent = message;
            if (message) {
                messageBox.style.display = 'block';
                if (type === 'error') {
                    messageBox.style.backgroundColor = '#f8d7da';
                    messageBox.style.color = '#721c24';
                    messageBox.style.borderColor = '#f5c6cb';
                } else {
                    messageBox.style.backgroundColor = '#d4edda';
                    messageBox.style.color = '#155724';
                    messageBox.style.borderColor = '#c3e6cb';
                }
            } else {
                messageBox.style.display = 'none';
            }
        }
    </script>
</body>
</html>
"""

# Salvar esse conteúdo como o index.html
frontend_index_path = "/mnt/data/thumbnail-generator-api/frontend/index.html"
with open(frontend_index_path, "w") as f:
    f.write(updated_frontend_html.strip())

# Compactar novamente
zip_final_path = "/mnt/data/thumbnail-generator-api-final.zip"
with zipfile.ZipFile(zip_final_path, 'w') as zipf:
    for root, dirs, files in os.walk("/mnt/data/thumbnail-generator-api"):
        for file in files:
            full_path = os.path.join(root, file)
            relative_path = os.path.relpath(full_path, "/mnt/data/thumbnail-generator-api")
            zipf.write(full_path, arcname=relative_path)

zip_final_path
