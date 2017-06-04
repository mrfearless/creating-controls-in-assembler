# The HotMenu Quest

So after my epiphany about controls being painted blobs and using smoke and mirrors to display how it worked, I began my quest to discover more, and how to create a control for myself.

My earliest attempt was a control called **HotMenu** - which was a menu panel system based on the Steam client menus. For those of you that have not used Steam or are not aware of it:

> Steam is a digital distribution platform developed by Valve Corporation, which offers digital rights management \(DRM\), multiplayer gaming, video streaming and social networking services. Steam provides the user with installation and automatic updating of games on multiple computers, and community features such as friends lists and groups, cloud saving, and in-game voice and chat functionality.

The image below depicts the normal Steam client menu usage. The menu panel drops down to display a list of menu items to choose from. As the mouse moves over the menu items they are highlighted. Additionally when the menu bar is clicked on any menu bar item and the mouse then hovers over any of the other menu bar items, the menu panel dropdown automatically is displayed, again with its list of menu items:

![Steam Client Menus](/assets/SteamClientMenus.gif) ![](/assets/SteamClientMenuStatic.png)

So taking inspiration from the Steam client menu system and wanted to adapt it to a custom control in win32 assembler, I began the development of **HotMenu.**

It worked by creating a rectangle shape when the mouse moved over the menu bar area, the rectangle menu panel was drawn just below this menu bar and then it iterated through a list of menu items, which where in reality child static controls of text I created and spaced accordingly in a vertically manner. When the mouse was 'clicked' on a menu item and detected \(using `SS_NOTIFY`\) on one of these static controls, it sent the notification back to the parent window/dialog and thus it could be processed as a menu item that had been clicked.

Here is the prototype of the **HotMenu** control, with all the settings set to emulate the Steam client menu:

![The HotMenu Control](/assets/HotMenu.gif) ![](/assets/HotMenuStatic.png)

The **HotMenu** control had a number of properties that allowed the user to control the overall look and feel of the menu bar, menu panel and menu items. I added a few more features like optional background image for the menu panel, images for menu items and static text in places. I took inspiration from a number of web based menu systems and online retailers, hoping to expand the flexibility of the **HotMenu** control to accommodate all different possibilities.

It all worked, but the **HotMenu** control didn't handle cascading sub menus, and I always meant to go back to it at some point to add more features, but other projects and interests pulled my attention away. But it still served as a basis for my development of controls I would develop in future.

