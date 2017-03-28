Video_notes.md

Useful notes about vive video, modelines and stuff.

From vive EDID:

Modeline 	"Mode 4" 297.00 2160 2200 2220 2266 1200 1228 1230 1464 -hsync -vsync

The odroid-c2 kernel hdmi clock driver has a predefined clock rate of 297.60 MHz.

(amlogic_pll_rate_table hpll_phy_tbl[20])

No need to add a new custom pixel clock for vive support.
