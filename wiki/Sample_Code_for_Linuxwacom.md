---
title: Sample Code for Linuxwacom
permalink: wiki/Sample_Code_for_Linuxwacom/
layout: wiki
tags:
 - Linuxwacom
---

:\* [Getting Values Reported from Wacom
Devices](/wiki/Sample_Code_for_Linuxwacom#Getting_Values_Reported_from_Wacom_Devices "wikilink")

:\* [Utilizing Intuos3 or Cintiq 21UX Menu
Strips](/wiki/Sample_Code_for_Linuxwacom#Utilizing_Intuos3_or_Cintiq_21UX_Menu_Strips "wikilink")

Getting Values Reported from Wacom Devices
------------------------------------------

The following is a routine of getting valuator data from xidump.c, a
program that helps users

troubleshooting Wacom X driver. The whole source code can be viewed from
xidump.c source

        static int RawRunDefault(Display* pDisp, XDeviceInfo* pDevInfo)
        {
            XEvent event;
            XAnyEvent* pAny;
            struct timeval tv;
            double dStart, dNow;

            gettimeofday(&tv,NULL);
            dStart = tv.tv_sec + (double)tv.tv_usec / 1E6;

            while (1)
            {
                XNextEvent(pDisp,&event);

                pAny = (XAnyEvent*)&event;
                /* printf("event: type=%s\n",GetEventName(pAny->type)); */

                /* display time */
                gettimeofday(&tv,NULL);
                dNow = tv.tv_sec + (double)tv.tv_usec / 1E6;
                printf("%.8f: ",(dNow - dStart));

                if (pAny->type == gnInputEvent[INPUTEVENT_PROXIMITY_IN])
                    printf("Proximity In\n");
                else if (pAny->type == gnInputEvent[INPUTEVENT_PROXIMITY_OUT])
                    printf("Proximity Out\n");
                else if (pAny->type == gnInputEvent[INPUTEVENT_FOCUS_IN])
                    printf("Focus In\n");
                else if (pAny->type == gnInputEvent[INPUTEVENT_FOCUS_OUT])
                    printf("Focus Out\n");
                else if (pAny->type == gnInputEvent[INPUTEVENT_MOTION_NOTIFY])
                {
                    XDeviceMotionEvent* pMove = (XDeviceMotionEvent*)pAny;

                    /* ID and serial number are not reported from wacom_drv 
                     * due to valuator backward compatibility concern
                     */
                    int v = (pMove->axis_data[4]&0xffff0000) | 
                            ((pMove->axis_data[5]&0xffff0000)>>16);

                    printf("Motion: x=%+6d y=%+6d p=%4d tx=%+4d ty=%+4d "
                        "w=%+5d ID: %4d Serial: %11d \n",
                            pMove->axis_data[0],
                            pMove->axis_data[1],
                            pMove->axis_data[2],
                            (short)(pMove->axis_data[3]&0xffff),
                            (short)(pMove->axis_data[4]&0xffff),
                            (short)(pMove->axis_data[5]&0xffff),
                            (pMove->axis_data[3]&0xffff0000)>>16,
                            v);

                }
                else if ((pAny->type == gnInputEvent[INPUTEVENT_BTN_PRESS]) ||
                        (pAny->type == gnInputEvent[INPUTEVENT_BTN_RELEASE]))
                {
                    XDeviceButtonEvent* pBtn = (XDeviceButtonEvent*)pAny;
                    printf("Button: %d %s\n",pBtn->button,
                            pAny->type == gnInputEvent[INPUTEVENT_BTN_PRESS] ?
                                "DOWN" : "UP");
                }
                else
                {
                    printf("Event: %s\n",GetEventName(pAny->type));
                }

                /* flush data to terminal */
                fflush(stdout);
            }

            return 0;
        }

Utilizing Intuos3 or Cintiq 21UX Menu Strips
--------------------------------------------

'''

Mats Johannesson (aka Voluspa) offered an example for using the Tablet
Menus and Touch

Strips of Intuos3 and Cintiq 21UX tablets. Listed below is his version
0.09.

Mats' program has been obsolete since we now support keystrokes through
[xsetwacom](/wiki/Linux_Wacom_HOWTO#Command_Line_Configuration_Interface_(xsetwacom) "wikilink").

        /* Version 0.09 4 April 2005
         *
         * To compile (example in 2 steps):
         * gcc -O2 -fomit-frame-pointer -c expresskeys.c
         * gcc -s -L/usr/X11R6/lib -o expresskeys expresskeys.o -lX11 -lXi -lXext -lXtst
         *
         *+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
         * Important: If you use the linuxwacom-0.6.7-beta or in the future
         * released versions you must change the pad statement in X config
         * file to:
         *
         * Section "ServerLayout"
         * [...]
         * InputDevice "pad" # Intuos3, Cintiq 21UX or Graphire 4. It must NOT send core event
         * [...]
         * EndSection
         *
         * See: http://linuxwacom.sourceforge.net/index.php/howto/srvlayout
         *
         * If you use the old pad statement, any pad button press will jump the
         * mouse cursor to the upper left corner (0,0) when another tool isn't
         * in proximity.
         *+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
         *
         * Run example: expresskeys pad &
         * Which will push it into the background. It is safe to close the terminal
         * afterwards. Oh, and X _must_ be running... The name, "pad" here, is
         * how it's called in xorg.conf (the "Identifier" option).
         * 
         * Key configuration is easy to change in the "user config area" below.
         * Use the "xev" program to find keycodes or look them up somewhere...
         * I've set the Wacom Intuos3 defaults on both sides, which is:
         * Shift, Alt, Control and Space. Touch strips are mildly supported.
         */

        /* ++++++++++ Begin user config area ++++++++++ */

        #define HANDLE_PEN 0    /* Main switch: 1 = yes handle a pen, 0 = no please */
        #define PEN_NAME "stylus"   /* Identifier name as configured in xorg.conf */
        #define PEN_MODE "Absolute" /* The mode we should expect the pen to be in */
                    /* when starting this program. Default usually */
                    /* is Absolute. The "mousy" feeling is Relative */

        /* Now, on to the next area. Please walk this way madam!        */
        /*----------------------------------------------.           */
        struct program {        /*      |           */
            char *class_name;   /*      |           */
            int handle_touch;   /*      |           */
            int l_touch_up;     /*      |           */
            int l_touch_up_plus;    /*      |           */
            int l_touch_down;   /*      |           */
            int l_touch_down_plus;  /*      |           */
            int r_touch_up;     /*      |           */
            int r_touch_up_plus;    /*      |           */
            int r_touch_down;   /*      |           */
            int r_touch_down_plus;  /*      |           */
            int key_9;      /*      |           */
            int key_9_plus;     /*      |           */
            int key_10;     /*      |           */
            int key_10_plus;    /*      |           */
            int key_11;     /*  Nothing to see here     */
            int key_11_plus;    /*  madam... Please move        */
            int key_12;     /*  along.              */
            int key_12_plus;    /*      |           */
            int key_13;     /*      |           */
            int key_13_plus;    /*      |           */
            int key_14;     /*      |           */
            int key_14_plus;    /*      |           */
            int key_15;     /*      |           */
            int key_15_plus;    /*      |           */
            int key_16;     /*      |           */
            int key_16_plus;    /*      |           */
        } prog_list[] = {       /*      |           */
                        /*      |           */
        /*-------------------------------.      V           */
        /*                  Go further down, past
         *                  these comments for the
         *                  real configuration area.
         */
        /* Left ExpressKey Pad
        ------------ 
        |  |   |   |        Wacom Intuos3 defaults are:
        |  | 9 | T |
        |11|---| O |        Key 9  = (left) Shift   = keycode 50
        |  |10 | U |        Key 10 = (left) Alt = keycode 64
        |------| C |        Key 11 = (left) Control = keycode 37
        |  12  | H |        Key 12 = Space      = keycode 65
        ------------
        */
        /* Right ExpressKey Pad
        ------------ 
        |   |   |  |        Wacom Intuos3 defaults are:
        | T |13 |  |
        | O |---|15|        Key 13 = (left) Shift   = keycode 50
        | U |14 |  |        Key 14 = (left) Alt = keycode 64
        | C |------|        Key 15 = (left) Control = keycode 37
        | H |  16  |        Key 16 = Space      = keycode 65
        ------------
        */

        /* The top configuration (named "default") will be used with
         * all programs and their windows that are not specified in a
         * separate configuration below this one. Default keycodes are
         * the now famous Wacom Intuos3 ones. I've also set totally
         * non-destructive touch strip keys (only used if handle_touch
         * is altered to a "1" - without quotes) which are: Arrow-keys
         * Up/Down on the left strip and Arrow-keys Right/Left on the
         * right strip. Change direction of the movement by switching
         * the _up and _down values.
         * 
         * If you want a key to do pen mode changes, use the value 999
         * under the corresponding keylabel. To be able to switch mode
         * anywhere, each configuration must contain one 999 definition.
         * And, of course, the main HANDLE_PEN definition above must
         * first be set to "1" - without quotes.
         * 
         * Please don't alter or remove the commas (,) after the keycodes.
         * They _must_ be there just as written.
         */
        /*  Name    handle_touch */
        {"default", 0,
        /*      l_touch_up  l_touch_up_plus l_touch_down    l_touch_down_plus */
                98,     0,      104,        0,
        /*      r_touch_up  r_touch_up_plus r_touch_down    r_touch_down_plus */
                102,        0,      100,        0,
        /*      key_9       key_9_plus  key_10      key_10_plus */
                50,     0,      64,     0,
        /*      key_11      key_11_plus key_12      key_12_plus */
                37,     0,      65,     0,
        /*      key_13      key_13_plus key_14      key_14_plus */
                50,     0,      64,     0,
        /*      key_15      key_15_plus key_16      key_16_plus */
                37,     0,      65,     0   },

        /*
         * Gimp has the touch strips turned on by default. The keycodes are:
         * 20 = "+" = Gimp Zoom In. Left/Right touch strip up motion
         * 61 = "-" = Gimp Zoom Out. Left/Right touch strip down motion
         * Change direction of the movement by switching _up and _down values.
         */
        /*  Name    handle_touch */
        {"Gimp",    1,
        /*      l_touch_up  l_touch_up_plus l_touch_down    l_touch_down_plus */
                20,     0,      61,     0,
        /*      r_touch_up  r_touch_up_plus r_touch_down    r_touch_down_plus */
                20,     0,      61,     0,
        /*      key_9       key_9_plus  key_10      key_10_plus */
                50,     0,      64,     0,
        /*      key_11      key_11_plus key_12      key_12_plus */
                37,     0,      65,     0,
        /*      key_13      key_13_plus key_14      key_14_plus */
                50,     0,      64,     0,
        /*      key_15      key_15_plus key_16      key_16_plus */
                37,     0,      65,     0   },

        /* This is my private definition for the 3D program blender...
         */
        /*  Name    handle_touch */
        {"Blender", 1,
        /*      l_touch_up  l_touch_up_plus l_touch_down    l_touch_down_plus */
                102,        0,      100,        0,
        /*      r_touch_up  r_touch_up_plus r_touch_down    r_touch_down_plus */
                98,     0,      104,        0,
        /*      key_9       key_9_plus  key_10      key_10_plus */
                37,     0,      9,      0,
        /*      key_11      key_11_plus key_12      key_12_plus */
                50,     0,      23,     0,
        /*      key_13      key_13_plus key_14      key_14_plus */
                50,     30,     30,     0,
        /*      key_15      key_15_plus key_16      key_16_plus */
                999,        0,      65,     0   },

        /* I feel that an xterm is too important a window to have _any_
         * interference from the pad. But observe that I want to be able
         * to switch pen mode even with such a window in focus.
         */
        /*  Name    handle_touch */
        {"XTerm",   0,
        /*      l_touch_up  l_touch_up_plus l_touch_down    l_touch_down_plus */
                0,      0,      0,      0,
        /*      r_touch_up  r_touch_up_plus r_touch_down    r_touch_down_plus */
                0,      0,      0,      0,
        /*      key_9       key_9_plus  key_10      key_10_plus */
                0,      0,      0,      0,
        /*      key_11      key_11_plus key_12      key_12_plus */
                0,      0,      0,      0,
        /*      key_13      key_13_plus key_14      key_14_plus */
                0,      0,      0,      0,
        /*      key_15      key_15_plus key_16      key_16_plus */
                999,        0,      0,      0   },

        /* And that's how it's done. Just copy a section and tweak the
         * settings for a new program. Your machine speed and memory
         * sets the limit ;-)
         */
        /* ++++++++++ End user config area ++++++++++ */

        };
        #define NUM_LIST (sizeof prog_list / sizeof prog_list[0])

        #include <X11/Xlib.h>
        #include <X11/Xutil.h>
        #include <X11/extensions/XInput.h>
        #include <X11/extensions/XTest.h>
        #include <stdio.h>
        #include <stdlib.h>
        #include <ctype.h>
        #include <string.h>

        #define EXIT_OK 1
        #define EXIT_KO 0
        #define INVALID_EVENT_TYPE -1
        #define TOGGLE_PEN 999

        Bool check_xinput (Display *display);
        int find_device_info(Display *display, char *name, Bool only_extended);
        int register_events(Display *display, XDeviceInfo *info, char *dev_name);
        int use_events(Display *display);
        int toggle_pen_mode(Display *display, char *name);

        int pen_mode = 1;
        int elder_rotation = 4097;
        int old_rotation = 4097;
        int elder_throttle = 4097;
        int old_throttle = 4097;

        int main (int argc, char *argv[])
        {

           if (PEN_MODE == "Absolute") {
            pen_mode = Absolute;
           } else {
            pen_mode = Relative;
           }

           Display *display = XOpenDisplay(NULL);

           if (display == NULL) {
            fprintf(stderr, "Can not connect to X-Server\n");
            return EXIT_KO;
           }

           int event_base, error_base;
           int major_version, minor_version;
           if (!XTestQueryExtension (display, &event_base, &error_base,
                &major_version, &minor_version)) {
            fprintf (stderr, "XTest extension not supported on server\n");
            XCloseDisplay(display);
            return EXIT_KO;
           }

           if (!check_xinput(display)) {
            fprintf(stderr, "%s extension not present\n", INAME);
            XCloseDisplay(display);
            return EXIT_KO;
           }

           if (argc < 2) {
            fprintf(stderr, "Usage: expresskeys \n");
            fprintf(stderr, "Example: expresskeys pad &\n");
            XCloseDisplay(display);
            return EXIT_KO;
           }

           XDeviceInfo  *info;
           int      idx = 1;

           info = find_device_info(display, argv[idx], True);

           if (!info) {
            fprintf(stderr, "Unable to find device %s\n", argv[idx]);
            XCloseDisplay(display);     
            return EXIT_KO;
           }

           if (register_events(display, info, argv[idx])) {
            use_events(display);
           } else {
            fprintf(stderr, "No event registered...\n");
            XCloseDisplay(display);
            return EXIT_KO;
           }

           XCloseDisplay(display);
           return EXIT_OK;
        }

        Bool check_xinput (Display *display)
        {
           XExtensionVersion *version;
           Bool present;
            
           version = XGetExtensionVersion (display, INAME);
            
           if (version && (version != (XExtensionVersion*) NoSuchExtension)) {
            present = version->present;
            XFree(version);
            return present;
           } else {
            return False;
           }
        }   

        int find_device_info(Display *display, char *name, Bool only_extended)
        {
           XDeviceInfo  *devices; 
           int      loop;
           int      num_devices;
           int      len = strlen(name);
           Bool     is_id = True;
           XID      id = 0;
            
           for(loop=0; loopid);

           if (!device) {
            fprintf(stderr, "Unable to open device %s\n", dev_name);
            return 0;
           }

           if (device->num_classes > 0) {
            for (ip = device->classes, i=0; inum_classes; ip++, i++) {
               switch (ip->input_class) {

                case ButtonClass:
                DeviceButtonPress(device, button_press_type, event_list[number]); number++;
                DeviceButtonRelease(device, button_release_type, event_list[number]); number++;
                break;

                case ValuatorClass:
                DeviceMotionNotify(device, motion_type, event_list[number]); number++;
                break;

                default:
                break;
               }
            }
            if (XSelectExtensionEvent(display, root_win, event_list, number)) {
               fprintf(stderr, "Error selecting extended events\n");
               return 0;        
            }
           }
           return number;   
        }

        int use_events(Display *display)
        {
           XEvent Event;
           while(1) {
            XNextEvent(display, &Event);

            XClassHint *class_hint;
            class_hint = XAllocClassHint();

            Window focus_window = None;
            int focus_state;

            Window root, parent;
            Window *children;
            unsigned int num_children;

            struct program *p;
            int in_list = 0;

            XGetInputFocus(display, &focus_window, &focus_state);
            XQueryTree(display, focus_window, &root, &parent, &children, &num_children);
            XGetClassHint(display, focus_window, class_hint);

            if ((!class_hint->res_class) && (parent) && (focus_window != root)){
               XFree(class_hint->res_class);
               XFree(class_hint->res_name);
               XGetClassHint(display, parent, class_hint);
            }

            if ((focus_window == root) || (class_hint->res_class == NULL)){
               p = prog_list;
            }
            else {
               for (p = prog_list; p < prog_list + NUM_LIST; p++)
                if (strcmp (class_hint->res_class, p->class_name) == 0){
                   in_list = 1;
                   break;
                }
            }
                
            XFree(class_hint->res_class);
            XFree(class_hint->res_name);
            if (children) XFree((char *)children);

            if (!in_list){
               p = prog_list;
            }

            if (Event.type == motion_type) {

               if (p->handle_touch){
                int rotation;
                int throttle;
            
                XDeviceMotionEvent *motion = (XDeviceMotionEvent *) &Event;

                rotation = motion->axis_data[3];
                throttle = motion->axis_data[4];

                if (rotation > 1){
                   if ((rotation < old_rotation) && (old_rotation <= elder_rotation)){
                    if (p->l_touch_up){
                       XTestFakeKeyEvent(display, p->l_touch_up, True, CurrentTime);
                       if (p->l_touch_up_plus){
                        XTestFakeKeyEvent(display, p->l_touch_up_plus, True, CurrentTime);
                        XTestFakeKeyEvent(display, p->l_touch_up_plus, False, CurrentTime);
                       }
                       XTestFakeKeyEvent(display, p->l_touch_up, False, CurrentTime);
                    }
                   }
                   else if ((rotation > old_rotation) && (old_rotation >= elder_rotation)){
                    if (p->l_touch_down){
                       XTestFakeKeyEvent(display, p->l_touch_down, True, CurrentTime);
                       if (p->l_touch_down_plus){
                        XTestFakeKeyEvent(display, p->l_touch_down_plus, True, CurrentTime);
                        XTestFakeKeyEvent(display, p->l_touch_down_plus, False, CurrentTime);
                       }
                       XTestFakeKeyEvent(display, p->l_touch_down, False, CurrentTime);
                    }
                   }
                   elder_rotation = old_rotation;
                   old_rotation = rotation;
                }

                if (throttle > 1){
                   if ((throttle < old_throttle) && (old_throttle <= elder_throttle)){
                    if (p->r_touch_up){
                       XTestFakeKeyEvent(display, p->r_touch_up, True, CurrentTime);
                       if (p->r_touch_up_plus){
                        XTestFakeKeyEvent(display, p->r_touch_up_plus, True, CurrentTime);
                        XTestFakeKeyEvent(display, p->r_touch_up_plus, False, CurrentTime);
                       }
                       XTestFakeKeyEvent(display, p->r_touch_up, False, CurrentTime);
                    }
                   }
                   else if ((throttle > old_throttle) && (old_throttle >= elder_throttle)){
                    if (p->r_touch_down){
                       XTestFakeKeyEvent(display, p->r_touch_down, True, CurrentTime);
                       if (p->r_touch_down_plus){
                        XTestFakeKeyEvent(display, p->r_touch_down_plus, True, CurrentTime);
                        XTestFakeKeyEvent(display, p->r_touch_down_plus, False, CurrentTime);
                       }
                       XTestFakeKeyEvent(display, p->r_touch_down, False, CurrentTime);
                    }
                   }
                   elder_throttle = old_throttle;
                   old_throttle = throttle;
                }
               }
            }

            if (Event.type == button_press_type) {

               XDeviceButtonEvent *button = (XDeviceButtonEvent *) &Event;

               switch (button->button) {
                case 9:
                   if (p->key_9 == TOGGLE_PEN)
                    if (HANDLE_PEN)
                       toggle_pen_mode(display, PEN_NAME);
                    else
                       break;
                   else if (p->key_9)
                    XTestFakeKeyEvent(display, p->key_9, True, CurrentTime );
                   if (p->key_9_plus)
                    XTestFakeKeyEvent(display, p->key_9_plus, True, CurrentTime );
                   else
                    break;
                   break;
                case 10:
                   if (p->key_10 == TOGGLE_PEN)
                    if (HANDLE_PEN)
                       toggle_pen_mode(display, PEN_NAME);
                    else
                       break;
                   else if (p->key_10)
                    XTestFakeKeyEvent(display, p->key_10, True, CurrentTime );
                   if (p->key_10_plus)
                    XTestFakeKeyEvent(display, p->key_10_plus, True, CurrentTime );
                   else
                    break;
                   break;
                case 11:
                   if (p->key_11 == TOGGLE_PEN)
                    if (HANDLE_PEN)
                       toggle_pen_mode(display, PEN_NAME);
                    else
                       break;
                   else if (p->key_11)
                    XTestFakeKeyEvent(display, p->key_11, True, CurrentTime );
                   if (p->key_11_plus)
                    XTestFakeKeyEvent(display, p->key_11_plus, True, CurrentTime );
                   else
                    break;
                   break;
                case 12:
                   if (p->key_12 == TOGGLE_PEN)
                    if (HANDLE_PEN)
                       toggle_pen_mode(display, PEN_NAME);
                    else   
                       break;
                   else if (p->key_12)
                    XTestFakeKeyEvent(display, p->key_12, True, CurrentTime );
                   if (p->key_12_plus)
                    XTestFakeKeyEvent(display, p->key_12_plus, True, CurrentTime );
                   else
                    break;
                   break;
                case 13:
                   if (p->key_13 == TOGGLE_PEN)
                    if (HANDLE_PEN)
                       toggle_pen_mode(display, PEN_NAME);
                    else
                       break;
                   else if (p->key_13)
                    XTestFakeKeyEvent(display, p->key_13, True, CurrentTime );
                   if (p->key_13_plus)
                    XTestFakeKeyEvent(display, p->key_13_plus, True, CurrentTime );
                   else
                    break;
                   break;
                case 14:
                   if (p->key_14 == TOGGLE_PEN)
                    if (HANDLE_PEN)
                       toggle_pen_mode(display, PEN_NAME);
                    else
                       break;
                   else if (p->key_14)
                    XTestFakeKeyEvent(display, p->key_14, True, CurrentTime );
                   if (p->key_14_plus)
                    XTestFakeKeyEvent(display, p->key_14_plus, True, CurrentTime );
                   else
                    break;
                   break;
                case 15:
                   if (p->key_15 == TOGGLE_PEN)
                    if (HANDLE_PEN)
                       toggle_pen_mode(display, PEN_NAME);
                    else
                       break;
                   else if (p->key_15)
                    XTestFakeKeyEvent(display, p->key_15, True, CurrentTime );
                   if (p->key_15_plus)
                    XTestFakeKeyEvent(display, p->key_15_plus, True, CurrentTime );
                   else
                    break;
                   break;
                case 16:
                   if (p->key_16 == TOGGLE_PEN)
                    if (HANDLE_PEN)
                       toggle_pen_mode(display, PEN_NAME);
                    else
                       break;
                   else if (p->key_16)
                    XTestFakeKeyEvent(display, p->key_16, True, CurrentTime );
                   if (p->key_16_plus)
                    XTestFakeKeyEvent(display, p->key_16_plus, True, CurrentTime );
                   else
                    break;
                   break;           
                default:
                   break;
               }
            }

            if (Event.type == button_release_type) {
               XDeviceButtonEvent *button = (XDeviceButtonEvent *) &Event;

                   switch (button->button) {
                case 9:
                   if (p->key_9 == TOGGLE_PEN)
                    break;
                   else if (p->key_9_plus)
                    XTestFakeKeyEvent(display, p->key_9_plus, False, CurrentTime );
                   if (p->key_9)
                    XTestFakeKeyEvent(display, p->key_9, False, CurrentTime );
                   else
                    break;
                   break;
                case 10:
                   if (p->key_10 == TOGGLE_PEN)     
                    break;
                   else if (p->key_10_plus)
                    XTestFakeKeyEvent(display, p->key_10_plus, False, CurrentTime );
                   if (p->key_10)
                    XTestFakeKeyEvent(display, p->key_10, False, CurrentTime );
                   else
                    break;
                   break;
                case 11:
                   if (p->key_11 == TOGGLE_PEN)
                    break;
                   else if (p->key_11_plus)
                    XTestFakeKeyEvent(display, p->key_11_plus, False, CurrentTime );
                   if (p->key_11)
                    XTestFakeKeyEvent(display, p->key_11, False, CurrentTime );
                   else
                    break;
                   break;
                case 12:
                   if (p->key_12 == TOGGLE_PEN)
                    break;
                   else if (p->key_12_plus)
                    XTestFakeKeyEvent(display, p->key_12_plus, False, CurrentTime );
                   if (p->key_12)
                    XTestFakeKeyEvent(display, p->key_12, False, CurrentTime );
                   else
                    break;
                   break;
                case 13:
                   if (p->key_13 == TOGGLE_PEN)
                    break;
                   else if (p->key_13_plus)     
                    XTestFakeKeyEvent(display, p->key_13_plus, False, CurrentTime );
                   if (p->key_13)
                    XTestFakeKeyEvent(display, p->key_13, False, CurrentTime );
                   else
                    break;
                   break;
                case 14:
                   if (p->key_14 == TOGGLE_PEN)
                    break;
                   else if (p->key_14_plus)
                    XTestFakeKeyEvent(display, p->key_14_plus, False, CurrentTime );
                   if (p->key_14)
                    XTestFakeKeyEvent(display, p->key_14, False, CurrentTime );
                   else
                    break;
                   break;
                case 15:
                   if (p->key_15 == TOGGLE_PEN)
                    break;
                   else if (p->key_15_plus)
                    XTestFakeKeyEvent(display, p->key_15_plus, False, CurrentTime );
                   if (p->key_15)
                    XTestFakeKeyEvent(display, p->key_15, False, CurrentTime );
                   else
                    break;
                   break;
                case 16:
                   if (p->key_16 == TOGGLE_PEN)
                    break;
                   else if (p->key_16_plus)
                    XTestFakeKeyEvent(display, p->key_16_plus, False, CurrentTime );
                   if (p->key_16)
                    XTestFakeKeyEvent(display, p->key_16, False, CurrentTime );
                   else
                    break;
                   break;
                default:
                   break;
               }
            }
           }
        }

        int toggle_pen_mode(Display *display, char *name)
        {
           XDeviceInfo  *info;
           XDevice      *device;

           info = find_device_info(display, PEN_NAME, True);

           if (!info) {
            fprintf(stderr, "unable to find device %s\n", PEN_NAME);
            return 0;
           }

           if (pen_mode == Absolute) {
            pen_mode = Relative;
           } else {
            pen_mode = Absolute;
           }

           device = XOpenDevice(display, info->id);

           if (device) {
            XSetDeviceMode(display, device, pen_mode);
            return 0;
           } else {
            fprintf(stderr, "Unable to open device %s\n", PEN_NAME);
            return 0;
           }
        }

<center>
<small> Copyright (C) 2002-2011 - LinuxWacom

This website and its contents are licensed under the GNU GENERAL PUBLIC
LICENSE. </small>

</center>
