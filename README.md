package com.example.schemblockplacer.commands;

import com.example.schemblockplacer.SchematicBlockPlacer;
import com.mojang.brigadier.Command;
import net.minecraft.commands.Commands;
import net.minecraft.network.chat.Component;
import net.minecraft.world.item.ItemStack;
import net.minecraftforge.event.RegisterCommandsEvent;
import net.minecraftforge.eventbus.api.SubscribeEvent;

public class CommandSchemDelete {
    @SubscribeEvent
    public static void onCommandRegister(RegisterCommandsEvent event) {
        event.getDispatcher().register(
            Commands.literal("2delete")
                .executes(ctx -> {
                    if (ctx.getSource().getPlayer() == null) {
                        ctx.getSource().sendFailure(
                            Component.literal("§cКоманда доступна только игрокам!")
                        );
                        return 0;
                    }

                    ItemStack heldItem = ctx.getSource().getPlayer().getMainHandItem();
                    if (heldItem.isEmpty()) {
                        ctx.getSource().sendFailure(
                            Component.literal("§cДержите блок в руке!")
                        );
                        return 0;
                    }

                    String blockId = heldItem.getItem().getRegistryName().toString();
                    if (blockId == null) {
                        ctx.getSource().sendFailure(
                            Component.literal("§cНевозможно определить ID блока")
                        );
                        return 0;
                    }

                    if (SchematicBlockPlacer.BLOCK_TO_SCHEM.containsKey(blockId)) {
                        String removedSchem = SchematicBlockPlacer.BLOCK_TO_SCHEM.remove(blockId);
                        ctx.getSource().sendSuccess(
                            Component.literal("§aПривязка удалена: §e" + blockId + " §7→ §f" + removedSchem),
                            false
                        );
                        SchematicBlockPlacer.saveBindings();
                    } else {
                        ctx.getSource().sendFailure(
                            Component.literal("§cДля блока §e" + blockId + " §cнет привязки!")
                        );
                    }
                    return Command.SINGLE_SUCCESS;
                })
        );
    }
}
