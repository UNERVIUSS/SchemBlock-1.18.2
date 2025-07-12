plugins {
    id 'eclipse'
    id 'idea'
    id 'net.minecraftforge.gradle' version '5.1.+'
}

version = '1.0'
group = 'com.example.schemblockplacer'

base {
    archivesName = 'SchemBlockPlacer'
}

java.toolchain.languageVersion = JavaLanguageVersion.of(17)

minecraft {
    mappings channel: 'official', version: '1.18.2'
    runs {
        client {
            workingDirectory project.file('run')
            property 'forge.logging.markers', 'REGISTRIES'
            property 'forge.logging.console.level', 'debug'
            mods {
                schemblockplacer {
                    source sourceSets.main
                }
            }
        }
    }
}

repositories {
    maven {
        url "https://maven.minecraftforge.net"
    }
}

dependencies {
    minecraft 'net.minecraftforge:forge:1.18.2-40.2.9'
}

tasks.named('processResources', ProcessResources).configure {
    var replaceProperties = [
            version: version
    ]
    inputs.properties replaceProperties
    filesMatching(['META-INF/mods.toml']) {
        expand replaceProperties + [project: project]
    }
}
