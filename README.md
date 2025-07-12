package com.example.schemblockplacer.commands;

import com.example.schemblockplacer.SchematicBlockPlacer;
import com.mojang.brigadier.Command;
import net.minecraft.commands.Commands;
import net.minecraft.network.chat.Component;
import net.minecraftforge.event.RegisterCommandsEvent;
import net.minecraftforge.eventbus.api.SubscribeEvent;

public class CommandSchemList {
    @SubscribeEvent
    public static void onCommandRegister(RegisterCommandsEvent event) {
        event.getDispatcher().register(
            Commands.literal("2list")
                .executes(ctx -> {
                    if (SchematicBlockPlacer.BLOCK_TO_SCHEM.isEmpty()) {
                        ctx.getSource().sendSuccess(
                            Component.literal("§6Нет привязанных схем."),
                            false
                        );
                    } else {
                        ctx.getSource().sendSuccess(
                            Component.literal("§a=== Привязанные схемы ==="),
                            false
                        );
                        
                        SchematicBlockPlacer.BLOCK_TO_SCHEM.forEach((block, schem) -> 
                            ctx.getSource().sendSuccess(
                                Component.literal("§e- §b" + block + " §7→ §f" + schem),
                                false
                            )
                        );
                        
                        ctx.getSource().sendSuccess(
                            Component.literal("§aВсего: §e" + SchematicBlockPlacer.BLOCK_TO_SCHEM.size()),
                            false
                        );
                    }
                    return Command.SINGLE_SUCCESS;
                })
        );
    }
}
