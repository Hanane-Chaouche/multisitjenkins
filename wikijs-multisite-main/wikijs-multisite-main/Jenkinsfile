pipeline {
  agent any  // Utilise n'importe quel agent Jenkins disponible

  environment {
    // ID de la clé SSH enregistrée dans Jenkins Credentials
    SSH_KEY_ID = 'cle-ssh-jenkins'

    // Paramètres de ta machine distante (VM)
    REMOTE_HOST = '4.206.99.81'        // IP publique ou DNS de ta VM
    REMOTE_USER = 'azureuser'          // Nom d'utilisateur de la VM
    REMOTE_DIR  = '/opt/wikijs-deploy' // Dossier sur la VM pour déployer le projet
  }

  stages {
    stage('Cloner dépôt localement (facultatif)') {
      steps {
        // Cela clone le dépôt dans l'espace de travail Jenkins (utile pour voir les fichiers dans Jenkins)
        git branch: 'main', url: 'https://github.com/Hanane-Chaouche/multisitjenkins.git'
      }
    }

    stage('Déployer sur la VM distante') {
      steps {
        // Active l'agent SSH avec la clé configurée dans Jenkins
        sshagent (credentials: ["${env.SSH_KEY_ID}"]) {
          sh """
            echo "Connexion à la VM distante ${REMOTE_HOST}..."
            
            ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} << 'EOF'

              echo "🛠 Vérification du dossier de déploiement..."
              if [ -d ${REMOTE_DIR}/.git ]; then
                echo "📦 Le projet existe, mise à jour..."
                cd ${REMOTE_DIR}
                git pull origin main
              else
                echo "📁 Création du dossier de projet et initialisation Git..."
                sudo mkdir -p ${REMOTE_DIR}
                sudo chown -R ${REMOTE_USER}:${REMOTE_USER} ${REMOTE_DIR}
                cd ${REMOTE_DIR}
                git init
                git config --global --add safe.directory ${REMOTE_DIR}
                git remote add origin https://github.com/Hanane-Chaouche/multisitjenkins.git
                git pull origin main
              fi

              echo "🚀 Lancement des services Docker..."
              docker compose -f instances/wiki1/docker-compose.yml up -d
              docker compose -f instances/wiki2/docker-compose.yml up -d
              docker compose -f instances/wiki-public/docker-compose.yml up -d
              docker compose -f nginx/docker-compose.yml up -d

              echo "✅ Déploiement terminé avec succès !"

            EOF
          """
        }
      }
    }
  }
}
