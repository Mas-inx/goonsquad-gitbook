# Installation

1. Download the asset through the Cfx.re account that owns it. Run the server with a license key that has access to the asset and with OneSync enabled.
2. Extract the resource as **gs-lumacafe** inside your server's resources directory. Keep the whole folder, including **stream/site/**, **lift/web/** and both root XML files.
3. Start any optional framework, target or notification provider you use before LUMA.
4. Add this line to **server.cfg**.

```cfg
ensure gs-lumacafe
```

5. Restart the server and reconnect. Visit **vector3(-376.0, 271.4, 84.28)** using your existing admin tools.

The two lift displays are bundled. Installation requires no UI build, database import or framework.

## Check the venue

- Approach from the street and check the entrance, exterior furniture, parking and collision.
- Walk through the interior and take the lift between **G**, **1**, **2** and **R**. With a second player, compare the landing display and door state and ride together.
- Check the rooftop pool, then take the lift back indoors and check that the interior stays visible.
- If you use job rules, test a player who meets the rule and a player who does not.
- Restart the resource while nearby and verify that the lift returns to the configured starting floor and its controls reappear.

If you already stream a map for this site or load a custom water table, review [Troubleshooting](troubleshooting.md#overlapping-maps-and-water) before enabling LUMA.
