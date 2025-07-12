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
                        ctx.getSource().sendFailure(Component.literal("§cТолько для игроков!"));
                        return 0;
                    }

                    ItemStack heldItem = ctx.getSource().getPlayer().getMainHandItem();
                    if (heldItem.isEmpty()) {
                        ctx.getSource().sendFailure(Component.literal("§cДержите блок в руке!"));
                        return 0;
                    }

                    String blockId = heldItem.getItem().getRegistryName().toString();
                    if (SchematicBlockPlacer.BLOCK_TO_SCHEM.remove(blockId) != null) {
                        ctx.getSource().sendSuccess(
                            Component.literal("§aПривязка для §e" + blockId + " §aудалена!"),
                            false
                        );
                        SchematicBlockPlacer.saveBindings();
                    } else {
                        ctx.getSource().sendFailure(
                            Component.literal("§cДля §e" + blockId + " §cнет привязки!")
                        );
                    }
                    return Command.SINGLE_SUCCESS;
                })
        );
    }
}
