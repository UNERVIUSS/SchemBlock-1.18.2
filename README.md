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
                            Component.literal("Нет привязанных схем."),
                            false
                        );
                    } else {
                        SchematicBlockPlacer.BLOCK_TO_SCHEM.forEach((block, schem) -> 
                            ctx.getSource().sendSuccess(
                                Component.literal(block + " → " + schem),
                                false
                            ));
                    }
                    return Command.SINGLE_SUCCESS;
                })
        );
    }
}
