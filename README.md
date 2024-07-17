/*:
 * @plugindesc Adds a Bestiary to the game where players can view details about enemies they have encountered.
 * @author YourName
 *
 * @param Bestiary Command Name
 * @text Bestiary Command Name
 * @desc The name of the command in the menu to open the Bestiary.
 * @default Bestiary
 *
 * @help
 * This plugin adds a Bestiary to the game. Players can view details about the enemies they have encountered.
 */

(() => {
    const parameters = PluginManager.parameters('Bestiary');
    const bestiaryCommandName = String(parameters['Bestiary Command Name'] || 'Bestiary');

    // Add Bestiary command to the menu
    const _Window_MenuCommand_addOriginalCommands = Window_MenuCommand.prototype.addOriginalCommands;
    Window_MenuCommand.prototype.addOriginalCommands = function() {
        _Window_MenuCommand_addOriginalCommands.call(this);
        this.addCommand(bestiaryCommandName, 'bestiary', true);
    };

    // Handle Bestiary scene
    const _Scene_Menu_createCommandWindow = Scene_Menu.prototype.createCommandWindow;
    Scene_Menu.prototype.createCommandWindow = function() {
        _Scene_Menu_createCommandWindow.call(this);
        this._commandWindow.setHandler('bestiary', this.commandBestiary.bind(this));
    };

    Scene_Menu.prototype.commandBestiary = function() {
        SceneManager.push(Scene_Bestiary);
    };

    // Bestiary Scene
    function Scene_Bestiary() {
        this.initialize(...arguments);
    }

    Scene_Bestiary.prototype = Object.create(Scene_MenuBase.prototype);
    Scene_Bestiary.prototype.constructor = Scene_Bestiary;

    Scene_Bestiary.prototype.initialize = function() {
        Scene_MenuBase.prototype.initialize.call(this);
    };

    Scene_Bestiary.prototype.create = function() {
        Scene_MenuBase.prototype.create.call(this);
        this.createBestiaryWindow();
    };

    Scene_Bestiary.prototype.createBestiaryWindow = function() {
        const rect = this.bestiaryWindowRect();
        this._bestiaryWindow = new Window_Bestiary(rect);
        this.addWindow(this._bestiaryWindow);
    };

    Scene_Bestiary.prototype.bestiaryWindowRect = function() {
        const wx = 0;
        const wy = 0;
        const ww = Graphics.boxWidth;
        const wh = Graphics.boxHeight;
        return new Rectangle(wx, wy, ww, wh);
    };

    // Bestiary Window
    function Window_Bestiary() {
        this.initialize(...arguments);
    }

    Window_Bestiary.prototype = Object.create(Window_Selectable.prototype);
    Window_Bestiary.prototype.constructor = Window_Bestiary;

    Window_Bestiary.prototype.initialize = function(rect) {
        Window_Selectable.prototype.initialize.call(this, rect);
        this.refresh();
        this.activate();
    };

    Window_Bestiary.prototype.maxItems = function() {
        return $dataEnemies.length - 1;
    };

    Window_Bestiary.prototype.drawItem = function(index) {
        const enemy = $dataEnemies[index + 1];
        const rect = this.itemRect(index);
        this.drawText(enemy.name, rect.x, rect.y, rect.width);
    };

    Window_Bestiary.prototype.refresh = function() {
        this.contents.clear();
        this.drawAllItems();
    };
})();
