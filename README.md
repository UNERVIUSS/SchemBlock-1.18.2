package com.example.schemblockplacer.events;

import com.example.schemblockplacer.SchematicBlockPlacer;
import net.minecraft.world.level.block.Block;
import net.minecraftforge.event.entity.player.PlayerInteractEvent;
import net.minecraftforge.eventbus.api.SubscribeEvent;

public class BlockPlaceHandler {
    @SubscribeEvent
    public void onBlockPlace(PlayerInteractEvent.RightClickBlock event) {
        if (event.getWorld().isClientSide()) return;

        Block block = event.getWorld().getBlockState(event.getPos()).getBlock();
        String blockId = block.getRegistryName().toString();
        
        if (SchematicBlockPlacer.BLOCK_TO_SCHEM.containsKey(blockId)) {
            String schemName = SchematicBlockPlacer.BLOCK_TO_SCHEM.get(blockId);
            event.getPlayer().chat("/schem load " + schemName);
            event.getPlayer().chat("//paste -a");
        }
    }
}
