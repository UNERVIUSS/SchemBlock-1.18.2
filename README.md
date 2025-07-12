package com.example.schemblockplacer.commands;

import com.example.schemblockplacer.SchematicBlockPlacer;
import com.mojang.brigadier.Command;
import net.minecraft.commands.Commands;
import net.minecraft.network.chat.Component;
import net.minecraftforge.event.RegisterCommandsEvent;
import net.minecraftforge.eventbus.api.SubscribeEvent;

public class CommandSchem {
    @SubscribeEvent
    public static void onCommandRegister(RegisterCommandsEvent event) {
        event.getDispatcher().register(
            Commands.literal("2")
                .executes(ctx -> {
                    if (ctx.getSource().getPlayer() == null) return 0;
                    
                    String blockId = ctx.getSource().getPlayer()
                        .getMainHandItem()
                        .getItem()
                        .getRegistryName()
                        .toString();
                    
                    ctx.getSource().sendSuccess(
                        Component.literal("Введите название схемы для блока " + blockId),
                        false
                    );
                    return Command.SINGLE_SUCCESS;
                })
        );
    }
}
