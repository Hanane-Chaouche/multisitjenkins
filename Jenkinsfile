pipeline {
  agent any

  environment {
    // ID de la clé privée ajoutée dans Jenkins (Manage Jenkins > Credentials)
    SSH_KEY_ID = 'd5282778-63a2-4efc-a800-2122a66e28f7'

    // Configuration de ta VM
    REMOTE_HOST = '4.206.99.81'
    REMOTE_USER = 'azureuser'
    REMOTE_DIR  = '/opt/wikijs-deploy'
  }

  stages {
    stage('Préparer accès GitHub') {
      steps {
        // Ajoute la clé GitHub à known_hosts pour éviter l'erreur SSH
        sshagent (credentials: [env.SSH_KEY_ID]) {
          sh '''
            mkdir -p ~/.ssh
            ssh-keyscan -H github.com >> ~/.ssh/known_hosts
          '''
        }
      }
    }

    stage('Déployer sur la VM') {
      steps {
        sshagent (credentials: [env.SSH_KEY_ID]) {
          sh """
            echo 'Connexion à la VM distante...'

            ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} << 'EOF'

              echo "📁 Vérification du dossier de déploiement..."

              if [ -d ${REMOTE_DIR}/.git ]; then
                echo "📦 Dépôt déjà présent. Mise à jour..."
                cd ${REMOTE_DIR}
                git pull origin main
              else
                echo "🚀 Clonage initial du projet"
                sudo mkdir -p ${REMOTE_DIR}
                sudo chown -R ${REMOTE_USER}:${REMOTE_USER} ${REMOTE_DIR}
                cd ${REMOTE_DIR}
                git init
                git config --global --add safe.directory ${REMOTE_DIR}
                git remote add origin git@github.com:Hanane-Chaouche/multisitjenkins.git
                git pull origin main
              fi

              echo "🐳 Lancement des conteneurs Docker..."
              docker compose -f instances/wiki1/docker-compose.yml up -d
              docker compose -f instances/wiki2/docker-compose.yml up -d
              docker compose -f instances/wiki-public/docker-compose.yml up -d
              docker compose -f nginx/docker-compose.yml up -d

              echo "✅ Déploiement terminé avec succès"

            EOF
          """
        }
      }
    }
  }
}
