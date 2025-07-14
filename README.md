package unerviuss.schemblock.commands;

import com.mojang.brigadier.CommandDispatcher;
import com.mojang.brigadier.arguments.StringArgumentType;
import net.minecraft.commands.CommandSourceStack;
import net.minecraft.commands.Commands;
import unerviuss.schemblock.data.SchemData;

public class CommandBind {
    public static void register(CommandDispatcher<CommandSourceStack> dispatcher) {
        dispatcher.register(Commands.literal("2")
            .then(Commands.argument("schem", StringArgumentType.string())
                .executes(ctx -> {
                    var player = ctx.getSource().getPlayerOrException();
                    var item = player.getMainHandItem().getItem();
                    String schem = StringArgumentType.getString(ctx, "schem");
                    SchemData.bind(item, schem);
                    ctx.getSource().sendSuccess(() ->
                        net.minecraft.network.chat.Component.literal("Привязано: " + schem), false);
                    return 1;
                })));
    }
}

package unerviuss.schemblock.commands;

import com.mojang.brigadier.CommandDispatcher;
import net.minecraft.commands.CommandSourceStack;
import net.minecraft.commands.Commands;
import unerviuss.schemblock.data.SchemData;

public class CommandDelete {
    public static void register(CommandDispatcher<CommandSourceStack> dispatcher) {
        dispatcher.register(Commands.literal("2delete")
            .executes(ctx -> {
                var player = ctx.getSource().getPlayerOrException();
                var item = player.getMainHandItem().getItem();
                SchemData.unbind(item);
                ctx.getSource().sendSuccess(() ->
                    net.minecraft.network.chat.Component.literal("Привязка удалена"), false);
                return 1;
            }));
    }
}

package unerviuss.schemblock.commands;

import com.mojang.brigadier.CommandDispatcher;
import net.minecraft.commands.CommandSourceStack;
import net.minecraft.commands.Commands;
import unerviuss.schemblock.data.SchemData;

public class CommandList {
    public static void register(CommandDispatcher<CommandSourceStack> dispatcher) {
        dispatcher.register(Commands.literal("2list")
            .executes(ctx -> {
                SchemData.getAllBindings().forEach((item, schem) -> {
                    ctx.getSource().sendSuccess(() ->
                        net.minecraft.network.chat.Component.literal(item.toString() + " -> " + schem), false);
                });
                return 1;
            }));
    }
}

package unerviuss.schemblock.handler;

import unerviuss.schemblock.data.SchemData;
import net.minecraftforge.event.world.BlockEvent;
import net.minecraftforge.eventbus.api.SubscribeEvent;
import net.minecraft.server.level.ServerPlayer;
import net.minecraft.commands.CommandSourceStack;

public class BlockPlaceHandler {
    @SubscribeEvent
    public void onBlockPlace(BlockEvent.EntityPlaceEvent event) {
        if (!(event.getEntity() instanceof ServerPlayer player)) return;

        var item = event.getPlacedBlock().getBlock().asItem();
        String schem = SchemData.getBoundSchem(item);
        if (schem != null) {
            try {
                String cmd = String.format("schem load %s; setblock %d %d %d minecraft:air; paste",
                    schem,
                    event.getPos().getX(), event.getPos().getY(), event.getPos().getZ());
                player.getServer().getCommands().performCommand(
                    new CommandSourceStack(player, player.position(), player.getRotationVector(),
                    player.getLevel() instanceof net.minecraft.server.level.ServerLevel sl ? sl : null, 4,
                    player.getName().getString(), player.getDisplayName(),
                    player.getServer(), player), cmd);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}

package unerviuss.schemblock.data;

import net.minecraft.world.item.Item;
import net.minecraft.world.item.Items;

import java.util.HashMap;
import java.util.Map;

public class SchemData {
    public static Map<Item, String> bindings = new HashMap<>();

    public static void bind(Item item, String schem) {
        bindings.put(item, schem);
    }

    public static void unbind(Item item) {
        bindings.remove(item);
    }

    public static String getBoundSchem(Item item) {
        return bindings.get(item);
    }

    public static Map<Item, String> getAllBindings() {
        return bindings;
    }

    static {
        bindings.put(Items.GOLD_BLOCK, "castle.schem");
    }
}
