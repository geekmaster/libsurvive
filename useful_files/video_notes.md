Video_notes.md

Useful notes about vive video, modelines and stuff.

From vive EDID:

Modeline 	"Mode 4" 297.00 2160 2200 2220 2266 1200 1228 1230 1464 -hsync -vsync

Feed the vive modeline data to this:  https://github.com/olihey/c2_mode

#### Results (suggested patches to odroid kernel odroid-c2-rt-kernel-odroidc2-3-14-rt68.y):
___
``` c
['Modeline', '2160x1200_90.0', '297.00', '2160', '2200', '2220', '2266', '1200', '1228', '1230', '1464', '-hsync', '-vsync']



1) drivers/amlogic/clk/gxbb_hdmi_clk.c: (around line 125)

    HPLL_FVCO_RATE(2970000, 0x3d, 0x1, 0, 0),



2) drivers/amlogic/clk/gxbb_hdmi_clk.c: (around line 150)

    VID_CLK(297000, 2970000, 0, 1, DIV_5, 1),



3) drivers/amlogic/clk/gxbb_hdmi_clk.c: (around line 460)

    case 2970000:
        writel(0x5800023d, hiu_base + HHI_HDMI_PLL_CNTL);
        writel(0x00000000, hiu_base + HHI_HDMI_PLL_CNTL2);
        writel(0x0d5c5091, hiu_base + HHI_HDMI_PLL_CNTL3);
        writel(0x801da72c, hiu_base + HHI_HDMI_PLL_CNTL4);
        writel(0x71486980, hiu_base + HHI_HDMI_PLL_CNTL5);
        writel(0x00000e55, hiu_base + HHI_HDMI_PLL_CNTL6);
        set_pll(rate_tbl);
        pr_info("hpll reg: 0x%x\n",
        	readl(hiu_base + HHI_HDMI_PLL_CNTL));
        hdmi_update_bits(HHI_HDMI_PLL_CNTL2, 0xffff, 0x4e00);
        break;



4) drivers/amlogic/clk/gxbb_hdmi_clk.c: (around line 790)

        CTS_XXX_TBL(297000, 297000, 1, 1),



5) drivers/amlogic/clk/gxbb_hdmi_clk.c: (around line 830)

        CTS_XXX_TBL(297000, 297000, 1, 1),



6) drivers/amlogic/display/vout/tv_vout.h: (around line 120)

        {TVMODE_2160x1200p90hz, VMODE_2160x1200p90hz},



7) drivers/amlogic/display/vout/tv_vout.h: (around line 1080)

    {
        .name              = "2160x1200p90hz",
        .mode              = TVMODE_2160x1200p90hz,
        .width             = 2160,
        .height            = 1200,
        .field_height      = 1200,
        .aspect_ratio_num  = 9,
        .aspect_ratio_den  = 5,
        .sync_duration_num = 90,
        .sync_duration_den = 1,
        .video_clk         = 297000000,
    },



8) drivers/amlogic/display/vout/tvregs.h: (around line 440)

static const struct reg_s tvregs_vesa_2160x1200_enc[] = {
    {MREG_END_MARKER,            0      }
};



9) drivers/amlogic/hdmi/hdmi_common/hdmi_parameters.c: (around line 1193)

static struct hdmi_format_para fmt_para_vesa_2160x1200p90_9x5 = {
    .vic = HDMIV_2160x1200p90hz,
    .name = "2160x1200p90hz",
    .pixel_repetition_factor = 0,
    .progress_mode = 1,
    .scrambler_en = 0,
    .tmds_clk_div40 = 0,
    .tmds_clk = 297000,
    .timing = {
        .pixel_freq = 297000, // = (h_total * v_total) / 2
        .h_freq = 131670,
        .v_freq = 90000,
        .vsync_polarity = 0,
        .hsync_polarity = 0,
        .h_active = 2160,
        .h_total = 2266,
        .h_blank = 106,
        .h_front = 40,
        .h_sync = 20,
        .h_back = 46,
        .v_active = 1200,
        .v_total = 1464,
        .v_blank = 264,
        .v_front = 28,
        .v_sync = 2,
        .v_back = 234,
        .v_sync_ln = 1,
    },
};



10) drivers/amlogic/hdmi/hdmi_common/hdmi_parameters.c: (around line 1193)

        &fmt_para_vesa_2160x1200p90_9x5,



11) drivers/amlogic/hdmi/hdmi_tx_20/hdmi_tx_edid.c: (around line 1632)

        {"2160x1200p90hz", HDMIV_2160x1200p90hz},



12) drivers/amlogic/hdmi/hdmi_tx_20/hdmi_tx_main.c: (around line 986)

        "2160x1200p90hz",



13) drivers/amlogic/hdmi/hdmi_tx_20/hdmi_tx_video.c: (around line 611)

    {
        .VIC		= HDMIV_2160x1200p90hz,
        .color_prefer   = COLOR_SPACE_RGB444,
        .color_depth	= hdmi_color_depth_24B,
        .bar_info	= B_BAR_VERT_HORIZ,
        .repeat_time	= NO_REPEAT,
        .aspect_ratio   = TV_ASPECT_RATIO_16_9,
        .cc		= CC_ITU709,
        .ss		= SS_SCAN_UNDER,
        .sc		= SC_SCALE_HORIZ_VERT,
    },



14) drivers/amlogic/hdmi/hdmi_tx_20/hw/enc_cfg_hw.c: (around line 985)

static const struct reg_s tvregs_2160x1200p90hz[] = {
    {P_VENC_VDAC_SETTING, 0xff,},
    {P_ENCP_VIDEO_EN, 0,},
    {P_ENCI_VIDEO_EN, 0,},

    {P_ENCP_VIDEO_MODE, 0x4040,},
    {P_ENCP_VIDEO_MODE_ADV, 0x18,},

    {P_ENCP_VIDEO_MAX_PXCNT, 2265,},
    {P_ENCP_VIDEO_MAX_LNCNT, 1463,},
    {P_ENCP_VIDEO_HAVON_BEGIN, 46,},
    {P_ENCP_VIDEO_HAVON_END, 2219,},
    {P_ENCP_VIDEO_VAVON_BLINE, 234,},
    {P_ENCP_VIDEO_VAVON_ELINE, 1473,},
    {P_ENCP_VIDEO_HSO_BEGIN, 0,},
    {P_ENCP_VIDEO_HSO_END, 20,},
    {P_ENCP_VIDEO_VSO_BEGIN, 0x1E,},
    {P_ENCP_VIDEO_VSO_END, 0x32,},
    {P_ENCP_VIDEO_VSO_BLINE, 0x0,},
    {P_ENCP_VIDEO_VSO_ELINE, 2,},

    {P_ENCP_VIDEO_EN, 1,},
    {P_ENCI_VIDEO_EN, 0,},
    {MREG_END_MARKER, 0},
};



15) drivers/amlogic/hdmi/hdmi_tx_20/hw/enc_cfg_hw.c: (around line 1032)

        {HDMIV_2160x1200p90hz, tvregs_2160x1200p90hz},



16) drivers/amlogic/hdmi/hdmi_tx_20/hw/hdmi_tx_hw.c: (around line 360)

        {HDMIV_2160x1200p90hz, 24000, 2970000, 297000, 297000, -1, 297000},



17) drivers/amlogic/hdmi/hdmi_tx_20/hw/hdmi_tx_hw.c: (around line 1570)

    case HDMIV_2160x1200p90hz:
        INTERLACE_MODE      = 0;
        PIXEL_REPEAT_VENC   = 0;
        PIXEL_REPEAT_HDMI   = 0;
        ACTIVE_PIXELS       = 2160;
        ACTIVE_LINES        = 1200;
        LINES_F0            = 1464;
        LINES_F1            = 1464;
        FRONT_PORCH         = 40;
        HSYNC_PIXELS        = 20;
        BACK_PORCH          = 46;
        EOF_LINES           = 28;
        VSYNC_LINES         = 2;
        SOF_LINES           = 234;
        TOTAL_FRAMES        = 4;
        break;



18) include/linux/amlogic/hdmi_tx/hdmi_common.h: (around line 187)

        HDMIV_2160x1200p90hz,



19) include/linux/amlogic/vout/vinfo.h: (around line 77)

        VMODE_2160x1200p90hz,



20) include/linux/amlogic/vout/vinfo.h: (around line 145)

        TVMODE_2160x1200p90hz,



21) drivers/amlogic/display/logo/logo.c: (around line 77)

        {"2160x1200p90hz", TVMODE_2160x1200p90hz},



22) drivers/amlogic/display/osd/osd_fb.c: (around line 417)

    case TVMODE_2160x1200p90hz:
        fb_def_var[index].xres = 2160;
        fb_def_var[index].yres = 1200;
        fb_def_var[index].xres_virtual = 2160;
        fb_def_var[index].yres_virtual = 2400;
        fb_def_var[index].bits_per_pixel = 32;
        break;
```
___

As a matter of technical interest, this online calculator gives slightly different timing values for the same modeline:  http://www.epanorama.net/faq/vga2rgb/calc.html
