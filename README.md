package unerviuss.schemblock;

import unerviuss.schemblock.commands.*;
import unerviuss.schemblock.handler.BlockPlaceHandler;
import net.minecraftforge.common.MinecraftForge;
import net.minecraftforge.event.RegisterCommandsEvent;
import net.minecraftforge.fml.common.Mod;
import net.minecraftforge.fml.event.lifecycle.FMLClientSetupEvent;
import net.minecraftforge.fml.javafmlmod.FMLJavaModLoadingContext;

@Mod("schemblock")
public class SchemBlock {
    public SchemBlock() {
        FMLJavaModLoadingContext.get().getModEventBus().addListener(this::clientSetup);
        MinecraftForge.EVENT_BUS.register(new BlockPlaceHandler());
        MinecraftForge.EVENT_BUS.addListener(this::onRegisterCommands);
    }

    private void clientSetup(final FMLClientSetupEvent event) {
        // пока пусто
    }

    private void onRegisterCommands(RegisterCommandsEvent event) {
        CommandBind.register(event.getDispatcher());
        CommandDelete.register(event.getDispatcher());
        CommandList.register(event.getDispatcher());
        CommandShare.register(event.getDispatcher());
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
