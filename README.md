1. c4 e5 2. g3 Nf6 3. Bg2 d5 4. cxd5 Nxd5 5. Nf3 Nc6 6. Nc3 Nxc3 7. bxc3 Bc5 8. Bb2 f5 9. d4 exd4 10. Nxd4 Bxd4 11. cxd4 O-O 12. Qb3+ Rf7 13. Bd5 Qe7 14. Rc1 Nxd4 15. Bxf7+ Qxf7 16. Qxf7+ Kxf7 17. Rxc7+ Kf8 18. Bxd4 Be6 19. Bxg7+ Kg8 20. Ba1 Bxa2 21. f3 b5 22. Rg7+ Kf8 23. Rxh7 a5 24. Rh8+ Kf7 25. Rxa8 Ke6 26. h4 Kd5 27. h5 Kc4 28. h6 Kb3 29. h7 Kc2 30. h8=Q Kc1 31. Qb2#

1. e4 e5 2. Nf3 Nc6 3. Bc4 Na5 4. Nxe5 Nxc4 5. Nxc4 d5 6. exd5 Qxd5 7. Ne3 Qc6 8. O-O Nf6 9. b3 b5 10. Bb2 Be6 11. Nc3 Bc5 12. Ne2 Qe4 13. Bxf6 gxf6 14. Ng3 Qc6 15. Qh5 Rd8 16. Ngf5 Rxd2 17. Rad1 Bxe3 18. Nxe3 Rxd1 19. Rxd1 a5 20. Nf5 Rg8 21. Ne3 Rh8 22. c4 bxc4 23. bxc4 Bxc4 24. Nxc4 Qxc4 25. Qf3 Ke7 26. Re1+ Kf8 27. Qxf6 Rg8 28. Rd1 Rxg2+ 29. Kh1 Rg1+ 30. Rxg1 Qd5+ 31. f3 c5 32. Re1 Qxa2 33. Qh8#



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
