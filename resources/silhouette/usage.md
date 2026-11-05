# Usage

Approach the receptionist and select **Speak to Receptionist** through your configured target system. Dead players and players in vehicles cannot open the directory. Access rules are checked when opening and before a configured action.

Browse venues with the mouse or Up/Down arrows. Number keys select an entry's configured level; floor labels can remain hidden. Enter/E activates the available action. Escape closes, and Backspace closes when enabled. Clicking the speech bubble completes the current typed quote.

The default panel is informational: no primary/secondary actions or venue coordinates are configured. Add those in config before expecting GPS or gameplay buttons.

Configured waypoint actions can set GPS, play a sound, optionally draw a marker and close the directory after a delay. Arrival can clear an active waypoint. Other actions close the directory before running the configured command, event or export.

Opening hours and live busy/closed/coming-soon states update in the panel. These do not lock the building or start jobs, payments or treatments. Use the relevant gameplay and door-lock integrations for those features.

Closing during the opening delay cancels the operation. Resource stop releases NUI focus, cameras, local NPC/prop state, guidance tracking and blur. Check [Troubleshooting](troubleshooting.md) for target or camera problems.
