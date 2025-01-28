### Conseils pour la refonte du plugin Minecraft en Java 21 avec Maven pour Minecraft 1.21.1

#### **1. Configuration de la structure du projet avec Maven**
Organisez le projet pour qu'il respecte les standards de Maven. Voici une structure recommandée :

```
my-plugin
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com.example.myplugin
│   │   │       ├── MyPlugin.java
│   │   │       ├── commands
│   │   │       │   ├── CommandHandler.java
│   │   │       │   └── ReloadCommand.java
│   │   │       ├── listeners
│   │   │       │   ├── PlayerListener.java
│   │   │       │   └── MobListener.java
│   │   │       ├── tasks
│   │   │       │   └── ScheduledTask.java
│   │   │       └── utils
│   │   │           └── PluginUtils.java
│   │   ├── resources
│   │       └── plugin.yml
│   └── test
│       └── java
│           └── com.example.myplugin
│               └── MyPluginTest.java
```

### **2. Configuration du fichier `pom.xml`**
Voici un exemple de configuration de base pour Java 21 et Minecraft :

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>my-plugin</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
    </properties>

    <dependencies>
        <!-- Minecraft API -->
        <dependency>
            <groupId>org.spigotmc</groupId>
            <artifactId>spigot-api</artifactId>
            <version>1.21.1-R0.1-SNAPSHOT</version>
            <scope>provided</scope>
        </dependency>

        <!-- JUnit for Testing -->
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>5.10.0</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <repositories>
        <repository>
            <id>spigotmc-repo</id>
            <url>https://hub.spigotmc.org/nexus/content/repositories/snapshots/</url>
        </repository>
    </repositories>
</project>
```

### **3. Refactorisation et améliorations du code**

#### **3.1 Utilisation des nouvelles fonctionnalités de Java 21**
1. **Pattern Matching** : Simplifiez les `instanceof` avec des patterns.
   ```java
   if (event.getEntity() instanceof Player player) {
       player.sendMessage("Welcome back!");
   }
   ```

2. **Records** : Utilisez des `record` pour des classes immuables, comme les POJOs simples.
   ```java
   public record MobData(String name, int health) {}
   ```

3. **Classes scellées (Sealed)** : Utilisez des classes scellées pour des hiérarchies de types bien définies.
   ```java
   public sealed interface Mob permits Zombie, Skeleton {}

   public final class Zombie implements Mob {}
   public final class Skeleton implements Mob {}
   ```

4. **String Templates (Preview)** : Simplifiez les concaténations de chaînes avec les templates.
   ```java
   System.out.printf("Welcome, \{playerName}! You have \{score} points!\");
   ```

#### **3.2 Gestion des commandes**
Centralisez les commandes dans une classe gestionnaire :
```java
public class CommandHandler implements CommandExecutor {
    @Override
    public boolean onCommand(CommandSender sender, Command command, String label, String[] args) {
        if (label.equalsIgnoreCase("reload")) {
            if (sender.hasPermission("myplugin.reload")) {
                MyPlugin.getInstance().reloadConfig();
                sender.sendMessage(ChatColor.GREEN + "Plugin reloaded!");
            } else {
                sender.sendMessage(ChatColor.RED + "You do not have permission to perform this command.");
            }
            return true;
        }
        return false;
    }
}
```

#### **3.3 Gestion des événements**

Utilisez des listeners pour gérer les événements.
```java
public class PlayerListener implements Listener {

    @EventHandler
    public void onPlayerJoin(PlayerJoinEvent event) {
        event.getPlayer().sendMessage(ChatColor.YELLOW + "Welcome to the server, " + event.getPlayer().getName() + "!");
    }

}
```

### **4. Intégration des tests unitaires**
Créez des tests unitaires pour vérifier les principales fonctionnalités du plugin.
```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class MyPluginTest {

    @Test
    public void testCommandExecution() {
        String commandOutput = "Plugin reloaded!";
        assertEquals("Plugin reloaded!", commandOutput);
    }

}
```

### **5. Adaptations spécifiques pour Minecraft 1.21.1**
1. **API de Spigot** : Assurez-vous que le plugin utilise les nouvelles méthodes et classes introduites dans Spigot 1.21.1.
2. **Tests en jeu** : Installez le plugin sur un serveur Minecraft 1.21.1 pour valider les fonctionnalités.

### **6. Déploiement et documentation**
1. **Fichier `plugin.yml`**
   ```yaml
   name: MyPlugin
   main: com.example.myplugin.MyPlugin
   version: 1.0
   api-version: 1.21
   commands:
     reload:
       description: Reloads the plugin.
       permission: myplugin.reload
   ```

2. **Compilation et empaquetage**
   Utilisez Maven pour compiler et empaqueter le plugin :
   ```bash
   mvn clean package
   ```

3. **Documentation** : Incluez un fichier README expliquant les commandes et permissions disponibles.

