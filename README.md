# MCP Create Server

Un service de gestion dynamique de serveurs MCP qui crée, exécute et gère des serveurs Model Context Protocol (MCP) de manière dynamique. Ce service fonctionne lui-même comme un serveur MCP et lance/gère d'autres serveurs MCP comme processus enfants, permettant un écosystème MCP flexible.

<a href="https://glama.ai/mcp/servers/lnl6xjkkeq">
  <img width="380" height="200" src="https://glama.ai/mcp/servers/lnl6xjkkeq/badge" alt="Create Server MCP server" />
</a>

## Fonctionnalités clés

- Création et exécution dynamiques de code de serveur MCP
- Support pour TypeScript uniquement (support JavaScript et Python prévu pour les prochaines versions)
- Exécution d'outils sur les serveurs MCP enfants
- Mises à jour et redémarrages du code serveur
- Suppression des serveurs non nécessaires

## Installation

**Note : Docker est la méthode recommandée pour exécuter ce service**

### Installation Docker (Recommandée)

```bash
# Construire l'image Docker
docker build -t mcp-create .

# Exécuter le conteneur Docker
docker run -it --rm mcp-create
```

### Installation manuelle (TypeScript uniquement)

```bash
# Cloner le dépôt
git clone https://github.com/tesla0225/mcp-create.git
cd mcp-create

# Installer les dépendances
npm install

# Construire
npm run build

# Exécuter
npm start
```

### Test de l'installation locale

Après avoir apporté des modifications au code, vous pouvez le tester localement :

```bash
# Reconstruire après les modifications
npm run build

# Tester le serveur directement (il attendra une entrée du protocole MCP)
npm start

# Ou tester avec une commande echo simple
echo '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}' | npm start
```

### Utilisation de la version locale avec Claude Desktop

Pour utiliser votre version locale modifiée avec Claude Desktop, mettez à jour votre `claude_desktop_config.json` :

```json
{
  "mcpServers": {
    "mcp-create-local": {
      "command": "node",
      "args": ["./dist/index.js"],
      "cwd": "/chemin/vers/votre/mcp-create"
    }
  }
}
```

**Note :** Remplacez `/chemin/vers/votre/mcp-create` par le chemin réel vers votre dépôt local.

### Construction d'une image Docker avec les modifications locales

Pour créer une image Docker avec vos modifications locales :

```bash
# Construire l'image Docker avec vos modifications
docker build -t mcp-create-local .

# Tester l'image Docker
docker run -it --rm mcp-create-local

# Utiliser avec Claude Desktop
# Mettre à jour claude_desktop_config.json :
{
  "mcpServers": {
    "mcp-create-local": {
      "command": "docker",
      "args": ["run", "-i", "--rm", "mcp-create-local"]
    }
  }
}
```

## Intégration avec Claude Desktop

Ajoutez ce qui suit à votre fichier de configuration Claude Desktop (`claude_desktop_config.json`) :

```json
{
  "mcpServers": {
    "mcp-create": {
      "command": "docker",
      "args": ["run", "-i", "--rm", "mcp-create"]
    }
  }
}
```

## Outils disponibles

| Nom de l'outil | Description | Paramètres d'entrée | Sortie |
|-----------|-------------|-----------------|--------|
| create-server-from-template | Créer un serveur MCP à partir d'un modèle | language: string | { serverId: string, message: string } |
| execute-tool | Exécuter un outil sur le serveur | serverId: string<br>toolName: string<br>args: object | Résultat de l'exécution de l'outil |
| get-server-tools | Obtenir la liste des outils du serveur | serverId: string | { tools: ToolDefinition[] } |
| delete-server | Supprimer un serveur | serverId: string | { success: boolean, message: string } |
| list-servers | Obtenir la liste des serveurs en cours d'exécution | aucun | { servers: string[] } |

## Exemples d'utilisation

### Création d'un nouveau serveur

```json
{
  "name": "create-server-from-template",
  "arguments": {
    "language": "typescript"
  }
}
```

### Exécution d'un outil

```json
{
  "name": "execute-tool",
  "arguments": {
    "serverId": "ba7c9a4f-6ba8-4cad-8ec8-a41a08c19fac",
    "toolName": "echo",
    "args": {
      "message": "Bonjour, serveur MCP dynamique !"
    }
  }
}
```

Parfait ! Pour reconstruire l'image Docker avec tes modifications, voici les étapes :

```bash
docker build -t mcp-create-local .
```

Voici les étapes pour reconstruire ton image Docker avec tes modifications :

## 🐳 **Reconstruire l'image Docker**

```bash
# 1. Assure-toi d'être dans le répertoire du projet
cd /chemin/vers/ton/mcp-create

# 2. Construire l'image avec tes modifications
docker build -t mcp-create-local .

# 3. Tester la nouvelle image
docker run -it --rm mcp-create-local

# 4. Optionnel : Voir les images disponibles
docker images
```

## 🔄 **Workflow complet après modifications**

1. **Faire tes modifications** dans `index.ts`
2. **Reconstruire Docker** : `docker build -t mcp-create-local .`
3. **Tester** : `docker run -it --rm mcp-create-local`
4. **Mettre à jour Claude Desktop** si nécessaire

## 📝 **Configuration Claude Desktop mise à jour**

```json
{
  "mcpServers": {
    "mcp-create-local": {
      "command": "docker",
      "args": ["run", "-i", "--rm", "mcp-create-local"]
    }
  }
}
```

## 💡 **Conseils pratiques**

- **Tag différent** : Si tu veux garder l'ancienne version, utilise un tag différent :

  ```bash
  docker build -t mcp-create-local:v2 .
  ```

- **Nettoyage** : Supprimer les anciennes images non utilisées :

  ```bash
  docker image prune
  ```

- **Vérification** : Lister tes images pour confirmer :

  ```bash
  docker images | grep mcp-create
  ```

Tu veux que je t'aide avec une étape particulière ou tu as des questions sur le processus ?

## Spécifications techniques

- Node.js 18 ou supérieur
- TypeScript (requis)
- Dépendances :
  - @modelcontextprotocol/sdk: Implémentation client/serveur MCP
  - child_process (intégré Node.js): Gestion des processus enfants
  - fs/promises (intégré Node.js): Opérations sur les fichiers
  - uuid: Génération d'ID de serveur unique

## Considérations de sécurité

- **Restrictions d'exécution de code :** Considérez la mise en sandbox car le service exécute du code arbitraire
- **Limitations de ressources :** Définissez des limites sur la mémoire, l'utilisation CPU, le nombre de fichiers, etc.
- **Surveillance des processus :** Surveillez et terminez de force les processus zombies ou en fuite
- **Validation des chemins :** Validez correctement les chemins de fichiers pour prévenir les attaques de traversée de répertoires

## Licence

MIT
