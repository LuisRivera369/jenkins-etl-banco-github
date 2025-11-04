pipeline {
    agent any

    stages {
        stage('Clonar Repositorio') {
            steps {
                echo '✨ Clonando el repositorio público desde GitHub...'
                git branch: 'main', url: 'https://github.com/LuisRivera369/jenkins-etl-banco-github.git'
            }
        }

        stage('Instalar dependencias') {
            steps {
                bat '''
                    @echo off
                    echo 📦 Instalando dependencias de Python
                    
                    "C:\\Users\\luis_\\AppData\\Local\\Programs\\Python\\Python313\\python.exe" -m pip install ^
                    requests ^
                    matplotlib ^
                    seaborn ^
                    sqlalchemy ^
                    pyodbc
                '''
            }
        }

        stage('ETL - EXTRACTION') {
            steps {
                bat '''
                    @echo off
                    echo 🔍 Ejecutando extracción de datos...

                    "C:\\Users\\luis_\\AppData\\Local\\Programs\\Python\\Python313\\python.exe" ^ %WORKSPACE%\\extraction\\extract_data.py

                    echo ✅ Extracción completada.
                '''
            }
        }

        stage('ETL - TRANSFORM') {
            steps {
                bat '''
                    @echo off
                    echo 🛠️ Ejecutando transformación de datos...

                    "C:\\Users\\luis_\\AppData\\Local\\Programs\\Python\\Python313\\python.exe" ^ %WORKSPACE%\\transformation\\transform_data.py

                    echo ✅ Transformación completada.
                '''
            }
        }


        stage('ETL - REPORTS') {
            steps {
                bat '''
                    @echo off
                    echo 📊 Generando reportes...

                    "C:\\Users\\luis_\\AppData\\Local\\Programs\\Python\\Python313\\python.exe" ^ %WORKSPACE%\\reports\\report_generator.py

                    echo ✅ Reportes generados correctamente.
                '''
            }
        }

        stage('ETL - LOAD') {
            steps {
                bat '''
                    @echo off
                    echo 💾 Cargando datos a la base de datos...

                    "C:\\Users\\luis_\\AppData\\Local\\Programs\\Python\\Python313\\python.exe" ^
                    %WORKSPACE%\\load\\load_data.py

                    echo ✅ Carga completada exitosamente.
                '''
            }
        }
    }

    
    post {
        always {
            script {
                def buildStatus = currentBuild.currentResult
                echo "📩 Enviando correo con el estado del build (${buildStatus})..."
                enviarEmail(buildStatus)

                echo "✅ Correo enviado satisfactoriamente 🎉"
            }
        }
    }
}


def enviarEmail(String status) {
    def icon = ""
    def color = ""
    def message = ""
    
    switch (status) {
        case "SUCCESS":
            icon = "✅"
            color = "#4CAF50"
            message = "¡La compilación fue exitosa!"
            break
        case "FAILURE":
            icon = "❌"
            color = "#F44336"
            message = "La compilación falló 😞"
            break
        case "UNSTABLE":
            icon = "⚠️"
            color = "#FFC107"
            message = "El build es inestable ⚠️"
            break
        case "ABORTED":
            icon = "🚫"
            color = "#9E9E9E"
            message = "El build fue abortado 🚫"
            break
        default: // Manejar otros estados posibles
            icon = "❓"
            color = "#607D8B"
            message = "Estado del build desconocido: ${status}"
            break
    }

    def htmlContent = """
    <html>
    <head>
        <style>
            body {
                font-family: 'Segoe UI', Arial, sans-serif;
                background-color: #f4f4f4;
                margin: 0;
                padding: 0;
            }
            .container {
                background-color: #ffffff;
                max-width: 600px;
                margin: 40px auto;
                border-radius: 10px;
                box-shadow: 0 2px 8px rgba(0,0,0,0.1);
                overflow: hidden;
            }
            .header {
                background-color: ${color};
                color: white;
                text-align: center;
                padding: 20px;
                font-size: 24px;
                font-weight: bold;
            }
            .body {
                padding: 20px;
                color: #333;
            }
            .footer {
                text-align: center;
                padding: 15px;
                font-size: 12px;
                color: #777;
            }
            a {
                color: ${color};
                text-decoration: none;
                font-weight: bold;
            }
        </style>
    </head>
    <body>
        <div class="container">
            <div class="header">${icon} ${status}</div>
            <div class="body">
                <p>${message}</p>
                <p><strong>Job:</strong> ${env.JOB_NAME}</p>
                <p><strong>Build #:</strong> ${env.BUILD_NUMBER}</p>
                <p><strong>URL:</strong> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
            </div>
            <div class="footer">
                Jenkins Notification • ${new Date()}
            </div>
        </div>
    </body>
    </html>
    """

    emailext (
        subject: "${icon} Jenkins Build ${status} - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
        body: htmlContent,
        mimeType: 'text/html',
        to: 'luis.rivera.dev.99@gmail.com',
        attachLog: true
    )
}