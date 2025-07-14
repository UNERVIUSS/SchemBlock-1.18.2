package com.unerviuss.schemblock.commands;

import com.unerviuss.schemblock.data.SchemData;
import com.mojang.brigadier.CommandDispatcher;
import net.minecraft.commands.CommandSourceStack;
import net.minecraft.commands.Commands;
import net.minecraft.world.item.ItemStack;

public class TwoBind {
    public static void register(CommandDispatcher<CommandSourceStack> dispatcher) {
        dispatcher.register(
            Commands.literal("2")
                .then(Commands.argument("schemName", net.minecraft.commands.arguments.StringArgumentType.string())
                    .executes(ctx -> {
                        ItemStack held = ctx.getSource().getPlayerOrException().getMainHandItem();
                        String blockId = held.getItem().getRegistryName().toString();
                        String schem = net.minecraft.commands.arguments.StringArgumentType.getString(ctx, "schemName");

                        SchemData.bind(blockId, schem);
                        ctx.getSource().sendSuccess(() -> net.minecraft.network.chat.Component.literal("Bound block " + blockId + " to schematic: " + schem), false);
                        return 1;
                    })
                )
        );
    }
}

package com.unerviuss.schemblock.commands;

import com.unerviuss.schemblock.data.SchemData;
import com.mojang.brigadier.CommandDispatcher;
import net.minecraft.commands.CommandSourceStack;
import net.minecraft.commands.Commands;
import net.minecraft.world.item.ItemStack;

public class TwoDelete {
    public static void register(CommandDispatcher<CommandSourceStack> dispatcher) {
        dispatcher.register(
            Commands.literal("2delete")
                .executes(ctx -> {
                    ItemStack held = ctx.getSource().getPlayerOrException().getMainHandItem();
                    String blockId = held.getItem().getRegistryName().toString();
                    SchemData.delete(blockId);
                    ctx.getSource().sendSuccess(() -> net.minecraft.network.chat.Component.literal("Removed binding from block: " + blockId), false);
                    return 1;
                })
        );
    }
}

package com.unerviuss.schemblock.commands;

import com.unerviuss.schemblock.data.SchemData;
import com.mojang.brigadier.CommandDispatcher;
import net.minecraft.commands.CommandSourceStack;
import net.minecraft.commands.Commands;
import net.minecraft.network.chat.Component;

public class TwoList {
    public static void register(CommandDispatcher<CommandSourceStack> dispatcher) {
        dispatcher.register(
            Commands.literal("2list")
                .executes(ctx -> {
                    ctx.getSource().sendSuccess(() -> Component.literal("Bindings list:"), false);
                    SchemData.getAll().forEach((block, schem) -> {
                        ctx.getSource().sendSuccess(() -> Component.literal(block + " -> " + schem), false);
                    });
                    return 1;
                })
        );
    }
}

package com.unerviuss.schemblock.commands;

import com.unerviuss.schemblock.data.SchemData;
import com.mojang.brigadier.CommandDispatcher;
import net.minecraft.commands.CommandSourceStack;
import net.minecraft.commands.Commands;
import net.minecraft.network.chat.Component;
import net.minecraft.network.chat.ClickEvent;

public class TwoShare {
    public static void register(CommandDispatcher<CommandSourceStack> dispatcher) {
        dispatcher.register(
            Commands.literal("2share")
                .executes(ctx -> {
                    StringBuilder sb = new StringBuilder();
                    SchemData.getAll().forEach((block, schem) -> sb.append(block).append("->").append(schem).append(" "));

                    Component message = Component.literal("[Click to copy bindings]")
                            .withStyle(style -> style.withClickEvent(new ClickEvent(ClickEvent.Action.COPY_TO_CLIPBOARD, sb.toString())));

                    ctx.getSource().sendSuccess(() -> message, false);
                    return 1;
                })
        );
    }
}
