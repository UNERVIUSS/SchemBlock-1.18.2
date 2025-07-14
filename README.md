package com.unerviuss.schemblock;

import com.unerviuss.schemblock.commands.TwoBind;
import com.unerviuss.schemblock.commands.TwoDelete;
import com.unerviuss.schemblock.commands.TwoList;
import com.unerviuss.schemblock.commands.TwoShare;
import com.unerviuss.schemblock.handler.BlockPlaceHandler;
import net.minecraftforge.common.MinecraftForge;
import net.minecraftforge.event.RegisterCommandsEvent;
import net.minecraftforge.fml.common.Mod;

@Mod(SchemBlock.MODID)
public class SchemBlock {
    public static final String MODID = "schemblock";

    public SchemBlock() {
        MinecraftForge.EVENT_BUS.register(new BlockPlaceHandler());
        MinecraftForge.EVENT_BUS.addListener(this::onRegisterCommands);
    }

    private void onRegisterCommands(RegisterCommandsEvent event) {
        TwoBind.register(event.getDispatcher());
        TwoDelete.register(event.getDispatcher());
        TwoList.register(event.getDispatcher());
        TwoShare.register(event.getDispatcher());
    }
}

package com.unerviuss.schemblock.data;

import java.util.HashMap;
import java.util.Map;

public class SchemData {
    private static final Map<String, String> bindings = new HashMap<>();

    public static void bind(String blockId, String schemName) {
        bindings.put(blockId, schemName);
    }

    public static void delete(String blockId) {
        bindings.remove(blockId);
    }

    public static Map<String, String> getAll() {
        return bindings;
    }

    public static String getSchem(String blockId) {
        return bindings.get(blockId);
    }
}

package com.unerviuss.schemblock.handler;

import com.unerviuss.schemblock.data.SchemData;
import net.minecraft.core.BlockPos;
import net.minecraft.server.level.ServerPlayer;
import net.minecraft.world.level.block.Block;
import net.minecraftforge.event.world.BlockEvent;
import net.minecraftforge.eventbus.api.SubscribeEvent;

public class BlockPlaceHandler {
    @SubscribeEvent
    public void onBlockPlaced(BlockEvent.EntityPlaceEvent event) {
        if (!(event.getEntity() instanceof ServerPlayer player)) return;

        Block block = event.getPlacedBlock().getBlock();
        String blockId = block.getRegistryName().toString();
        String schem = SchemData.getSchem(blockId);

        if (schem != null) {
            BlockPos pos = event.getPos();

            // Заглушка: вставка схемы через команды
            player.server.getCommands().performCommand(player.createCommandSourceStack(), "schem load " + schem);
            player.server.getCommands().performCommand(player.createCommandSourceStack(),
                    "setblock " + pos.getX() + " " + pos.getY() + " " + pos.getZ() + " minecraft:air");
            player.server.getCommands().performCommand(player.createCommandSourceStack(), "paste");
        }
    }
}
