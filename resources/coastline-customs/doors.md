# Coastline Customs - doors

The garage doors and the parts store's side door are Coastline Customs' own door models, themed on both sides. Each is a
real GTA door (dynamic archetype with door physics, garage door type 5, GTA door skeleton and collision), so it opens
and closes through the game's door system. They sit on their MLO portals.

**Door-lock script:** add one door per row (ox_doorlock: model + coords, the garage doors as garage doors, lock them to
the mechanic job).

| Door | Model | Coords | Heading | Type |
|---|---|---|---|---|
| Detailing studio (bay 1) | `gs_cc_gdoor_detail` | vec3(-1654.100, -791.348, 11.363) | 140.0 | garage |
| Bay 2 | `gs_cc_gdoor_bay2` | vec3(-1660.994, -785.563, 11.363) | 140.0 | garage |
| Bay 3 | `gs_cc_gdoor_bay3` | vec3(-1667.888, -779.778, 11.363) | 140.0 | garage |
| Bay 4 | `gs_cc_gdoor_bay4` | vec3(-1674.783, -773.993, 11.363) | 140.0 | garage |
| Bay 5 | `gs_cc_gdoor_bay5` | vec3(-1681.677, -768.208, 11.363) | 140.0 | garage |
| Dyno lane 1 | `gs_cc_lsdoor_dyno1` | vec3(-1687.007, -763.670, 11.303) | 140.0 | garage |
| Dyno lane 2 | `gs_cc_lsdoor_dyno2` | vec3(-1690.838, -760.456, 11.303) | 140.0 | garage |
| Paint booth | `gs_cc_gdoor_paint` | vec3(-1696.071, -756.130, 11.363) | 140.0 | garage |
| Parts store side door | `gs_cc_stockdoor` | vec3(-1707.914, -746.192, 10.480) | 140.0 | normal (swing) |

The glass doors (lounge entrance, lounge to workshop, detailing studio, the four mezzanine rooms, the boss room's
terrace door, the dyno booth) are framed GTA push doors (`sf_prop_sf_door_office_l_01a` / `_r_01a`): they swing when
walked into, nothing to configure.


## Installation notes

Keep the internal model names as listed. Set job restrictions, default lock state and garage behavior in your door-lock resource, then test all nine custom doors in FiveM. The map itself does not register door locks or a mechanic job. Glass push doors move through game physics by default; add locks separately if your server requires restricted access.

See [Configuration & Integration](configuration.md) and [Troubleshooting](troubleshooting.md).
