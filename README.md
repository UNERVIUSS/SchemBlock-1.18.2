plugins {
    id 'eclipse'
    id 'idea'
    id 'net.minecraftforge.gradle' version '[6.0,6.2)'
}

version = '1.0'
group = 'com.example.schemblockplacer'

java {
    toolchain.languageVersion = JavaLanguageVersion.of(17)
}

minecraft {
    mappings channel: 'parchment', version: '2022.11.27-1.18.2'
    runs {
        client {
            workingDirectory project.file('run')
            property 'forge.logging.console.level', 'debug'
            mods {
                schemblockplacer {
                    source sourceSets.main
                }
            }
        }
    }
}

dependencies {
    minecraft 'net.minecraftforge:forge:1.18.2-40.2.9'
}

tasks.named('processResources', ProcessResources).configure {
    filesMatching('META-INF/mods.toml') {
        expand 'version': version
    }
}
