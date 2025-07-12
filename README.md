package com.example.schemblockplacer;

import com.example.schemblockplacer.commands.*;
import com.example.schemblockplacer.events.BlockPlaceHandler;
import net.minecraftforge.common.MinecraftForge;
import net.minecraftforge.fml.common.Mod;
import net.minecraftforge.fml.javafmlmod.FMLJavaModLoadingContext;
import net.minecraftforge.fml.event.lifecycle.FMLCommonSetupEvent;

import java.util.HashMap;
import java.util.Map;

@Mod("schemblockplacer")
public class SchematicBlockPlacer {
    public static final String MOD_ID = "schemblockplacer";
    public static final Map<String, String> BLOCK_TO_SCHEM = new HashMap<>();

    public SchematicBlockPlacer() {
        MinecraftForge.EVENT_BUS.register(new BlockPlaceHandler());
        FMLJavaModLoadingContext.get().getModEventBus().addListener(this::setupCommands);
    }

    private void setupCommands(FMLCommonSetupEvent event) {
        CommandSchem.register();
        CommandSchemList.register();
        CommandSchemDelete.register();
    }
}
