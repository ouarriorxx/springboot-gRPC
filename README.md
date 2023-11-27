# springboot-gRPC
Configuration du Serveur

# 1. Créer un Projet Spring Boot
Utilisez Spring Initializr ou toute autre méthode préférée pour créer un projet Spring Boot.

Initialisation du Projet Spring Boot

# 2. Ajouter les Dépendances Maven
Incluez les propriétés et les dépendances Maven suivantes dans votre fichier pom.xml :


<!-- Versions des Propriétés -->
<properties>
    <protobuf.version>3.23.4</protobuf.version>
    <protobuf-plugin.version>0.6.1</protobuf-plugin.version>
    <grpc.version>1.58.0</grpc.version>
</properties>

<!-- Dépendances -->
<dependency>
    <groupId>io.grpc</groupId>
    <artifactId>grpc-stub</artifactId>
    <version>${grpc.version}</version>
</dependency>
<dependency>
    <groupId>io.grpc</groupId>
    <artifactId>grpc-protobuf</artifactId>
    <version>${grpc.version}</version>
</dependency>
<dependency>
    <groupId>jakarta.annotation</groupId>
    <artifactId>jakarta.annotation-api</artifactId>
    <version>1.3.5</version>
    <optional>true</optional>
</dependency>
<dependency>
    <groupId>net.devh</groupId>
    <artifactId>grpc-spring-boot-starter</artifactId>
    <version>2.15.0.RELEASE</version>
</dependency>

<!-- Plugin pour les Buffers de Protocole -->
<<plugin>
    <groupId>com.github.os72</groupId>
    <artifactId>protoc-jar-maven-plugin</artifactId>
    <version>3.11.4</version>
    <executions>
        <execution>
            <phase>generate-sources</phase>
            <goals>
                <goal>run</goal>
            </goals>
            <configuration>
                <includeMavenTypes>direct</includeMavenTypes>
                <inputDirectories>
                    <include>src/main/resources</include>
                </inputDirectories>
                <outputTargets>
                    <outputTarget>
                        <type>java</type>
                        <outputDirectory>src/main/java</outputDirectory>
                    </outputTarget>
                    <outputTarget>
                        <type>grpc-java</type>
                        <pluginArtifact>io.grpc:protoc-gen-grpc-java:1.15.0</pluginArtifact>
                        <outputDirectory>src/main/java</outputDirectory>
                    </outputTarget>
                </outputTargets>
            </configuration>
        </execution>
    </executions>
</plugin>

# 3. Créer les Définitions de Service gRPC

Définissez votre service gRPC dans les fichiers .proto à l'intérieur du répertoire src/main/resources.

Exemple de stagiaire.proto :


syntax = "proto3";
option java_package = "com.a00n.grpc.stubs"; // replace this with your_package_name_gprc.stubs 

message Stagiaire {
  int id = 1;
  string nom = 2;
  string prenom = 3;
  int64 moisdebut = 4;
}
message Empty {}

service StudentService {
  rpc ListStagiaires(Empty) returns (ListStagiairesResponse);
  rpc GetStagiaire(GetStagiaireRequest) returns (Stagiaire);
  rpc CreateStagiaire(CreateStagiaireRequest) returns (Stagiaire);
  rpc UpdateStagiaire(Stagiaire) returns (Stagiaire);
  rpc DeleteStagiaire(DeleteStagiaireRequest) returns (DeleteStagiaireResponse);
}

message ListStagiairesResponse { repeated Stagiaire stagiaires = 1; }
message GetStagiaireRequest { int id = 1; }
message DeleteStagiaireRequest { int id = 1; }
message DeleteStagiaireResponse { string message = 1; }
message CreateStagiaireRequest {
  string nom = 1;
  string prenom = 2;
  int moisdebut = 3;
}


# 4. Créer une Entité JPA pour Stagiaire
@Entity
@Data
public class Stagiaire {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
    private String nom;
    private String prenom;
    private int moisdebut;

}

@Repository
public interface StagiaireRepository extends JpaRepository<Stagiaire, Id> {
}

# 5. Mapper gRPC Stagiaire avec JPA Stagiaire

@Component
public class StagiaireMapper {
    public com.a00n.grpc.stubs.StagiaireOuterClass.Stagiaire toGrpcStagiaire(Stagiaire stagiaire) {
        return com.a00n.grpc.stubs.StagiaireOuterClass.Stagiaire.newBuilder().setId(stagiaire.getId())
                .setFirstName(student.getNom())
                .setLastName(student.getPrenom())
                .setAge(student.getMoisdebut())
                .build();
    }

    public Stagiaire fromGrpcStagiaire(com.a00n.grpc.stubs.StagiaireOuterClass.Stagiaire stagiaire) {
        return new Stagiaire(stagiaire.getId(), stagiaire.getNom(), stagiaire.getPrenom(), stagiaire.getMoisdebut());
    }
}

# 6. Implémenter le Service

@Component
public class StagiaireMapper {
    public com.a00n.grpc.stubs.StagiaireOuterClass.Stagiaire toGrpcStagiaire(Stagiaire stagiaire) {
        return com.a00n.grpc.stubs.StagiaireOuterClass.Stagiaire.newBuilder().setId(stagiaire.getId())
                .setNom(stagiaire.getNom())
                .setLastPrenom(stagiaire.getPrenom())
                .setMoisdebut(stagiaire.getMoisdebut())
                .build();
    }

    public Stagiaire fromGrpcStagiaire(com.a00n.grpc.stubs.StagiaireOuterClassStagiaire stagiaire) {
        return new Stagiaire(stagiaire.getId(), stagiaire.getFirstNom(), stagiaire.getPrenom(), stagiaire.getMoisdebut());
    }
}
java
Copy code
// Classe GrpcStagiaireServiceImpl
7. Configuration du Serveur
Ajoutez les propriétés nécessaires à application.properties pour la configuration de la base de données.

properties
Copy code
# Configuration de la Base de Données
spring.datasource.url=jdbc:mysql://localhost:3306/grpc_stagiaire?createDatabaseIfNotExist=true
# Ajoutez d'autres propriétés selon les besoins
