<!--

 Perl Polybar Weather
 -----------------------------------------------
 Author: Mark Page [mark@very3.net]
 Modified: Sun Dec 22 17:07:53 2024 -0600
 Version: 24.12.22.177
 -----------------------------------------------

-->
# Arch/i3 Perl Polybar Weather

![Screenshot of Perl Poly Bar notification](https://very3.io/assets/img/ppw.png?_v=24.12.22.1342)

## About Perl Polybar Weather

This is a low-effort smattering of configs and scripts to provide a simple weather module for [i3](https://i3wm.org/)
under [Arch](https://archlinux.org/) with [Polybar](https://github.com/polybar/polybar) using the [OpenWeather](https://openweathermap.org/) API.
The module ships with a down-and-dirty `notification-launcher` that provides a weekly forecast pop-up via the system
notification daemon. The `notification-launcher` script can be executed from Polybar's click exec functions,
or via the shell (keyboard, mouse, event, etc.).

## To-Do
 * Improve error checking (as in *actually do some error checking*).
 * Add weather sources (currently limited to OpenWeather).

# Installation
> [!CAUTION]
> If your Polybar config dir is something other than `~/.config/polybar`, substitute that location in the instructions below.
> You'll also need to modify `app_path` variable in the `perl-polybar-weather` script!

 * Move to your Polybar config dir (usually ~/.config/polybar) and run:
 ```
 git clone git@github.com:verythree/perl-polybar-weather
 ```
 * Move to to the `perl-polybar-weather` folder, open the `config.ini` file in your favorite editor and set the location variables ([see below](#module-configuration)).
 * Depending on your distro, you may need to install the Perl JSON (libjson) modules:
 ```
 sudo pacman -S perl-json
 ```


## Fonts and Icons
We highly recommend using the [Weather Icons](https://erikflowers.github.io/weather-icons/) font:
 * Download the fonts: `wget https://github.com/erikflowers/weather-icons/archive/master.zip`
 * Unzip the archive: `unzip master.zip`
 * Install the fonts: `sudo cp -r weather-icons-master /usr/local/share/fonts/.`
 * Update the font cache: `sudo fc-cache -f`

You may need to let Polybar know which font(s) to use for the module. In the example below, we've assigned `font-2` for
labels, and added a few more for kicks. The fonts listed below are only an example of how to include fonts. You'll only
need to add the "Weather Icons" font, for the `font-1` configuration variable.

> [!WARNING]
> If the `font-1` variable is already assigned, you'll need to use the next available font number, and change the
> `label-font = 2` line mentioned in the section below to the number you picked.

```ini
font-0 = Roboto Bold:style=SemiBold:size=10;2
font-1 = Weather Icons:size=9;3
font-2 = Roboto:size=10;3
font-3 = Terminess Nerd Font Mono:size=18;4
```
> [!TIP]
> If you're using, or would like to use `xfce4-notifyd`, be sure to read the xfce4-notifyd sections in the
> [Additional notes](#additional-notes).


## Using this module with Polybar
> [!CAUTION]
> If your Polybar config dir is something other than `~/.config/polybar`, substitute that location in the instructions
> below. You'll also need to modify `app_path` variable in the `perl-polybar-weather` script!

### Module Configuration
The `config.ini` stores the location information needed for the OpenWeather API and the `notification-launcher` script.

> [!IMPORTANT]
> Take care to only change data on the right side of the "=" operators.

 * Move to your Perl Polybar Weather dir (usually ~/.config/polybar/perl-polybar-weather)
 * Open the `config.ini` in your editor of choice
    * __api_key__: OpenWeather API key   
      Visit https://home.openweathermap.org/users/sign_up, then get create your key by visiting https://home.openweathermap.org/api_keys.

    * __api_cid__: OpenWeather City ID   
      Visit: https://openweathermap.org/find. You'll need the 7-digit city code from the end of the URL.

    * __api_lat__ and __api_lon__: OpenWeather API latitude and longitude   
      To find your latitude and longitude, visit https://www.latlong.net/

    * __api_units__: Display units   
      Possible values are "imperial", "metric", and "standard"

    * __api_days__: Number of days to show in forecast   
      Possible values are 1-16

    * __divider__: Divider character shown in Polybar   
      Use any ASCII or multibyte divider character. Add spaces for padding if desired.

    * __browser__ and __browser_args__: Browser for services   
      Use a fully-qualified path if needed. If using a non-chromium based browser, leave it blank.

    * __notify_agent__: System notification daemon   
      If you're using a notification service agent other than notify-send

### Polybar config
> [!WARNING]
> If the `font-1` variable was already assigned in the previous steps, you'll need to change the `label-font = 2` below
> font number chose.

Add the following to your Polybar config. This assumes that your Polybar config lives at `~/.config/polybar/`.
```ini
[module/weather]
type = custom/script
exec = ~/.config/polybar/perl-polybar-weather/perl-polybar-weather
tail = false
interval = 60
label-font = 2
click-left = ~/.config/polybar/perl-polybar-weather/notification-launcher
```
> [!TIP]
> Don't forget to add the `weather` module to your desired location in the bar. Usually something like:
>  `modules-right = weather pulseaudio tray`.

### Calling the module
The module has 2 modes, `weather` and `forecast`. When calling the perl-polybar-weather script with no arguments,
it will use the `weather` option and return the current temperature and sky conditions. Using the `forecast`
argument returns an fprint-formatted 6-day forecast (or 5-day depending on the time of day) text block, suitable
for use with a system notification pop-up with a fixed-width font.

> [!IMPORTANT]
> Note: When calling `weather` mode with extended options, you *must* include the `weather` argument in the first
> position. The `forecast` argument does not have any available options at this time.

There are four `weather` options that can be returned:
 * __wind__: Wind speed and cardinal direction
 * __sky__: Sky conditions
 * __temp__: Current temperature
 * __temps__: Current and daily low/high temperatures

Using the Polybar config example given above as a reference, changing the line containing:
```
exec = ~/.config/polybar/perl-polybar-weather/perl-polybar-weather
```
To this:
```
exec = ~/.config/polybar/perl-polybar-weather/perl-polybar-weather weather temps-sky
```

Would display the current and daily low/high temperatures with the current sky conditions (like we're using
in the image above). Options may be chained in any order. For example, calling the module with:
```
exec = ~/.config/polybar/perl-polybar-weather/perl-polybar-weather weather wind-sky-temp-temps
```
-or-
```
exec = ~/.config/polybar/perl-polybar-weather/perl-polybar-weather weather sky-temp-temps-wind
```
-or-
```
exec = ~/.config/polybar/perl-polybar-weather/perl-polybar-weather weather temp-temps-wind-sky
```
etc...

would call all possible options, in argument order.



## Additional notes
### Modified i3 and Polybar setups
If you've got a modified i3/Polybar setup, either provided by you or your distro, your installation of this module will need to be adjusted
accordingly. If you're well-versed with ricing, there should be enough info in this guide to get you started. Otherwise, refer to your
distro's i3 configuration pages for more information. If you get lost, leave a message in the [discussion section](https://github.com/verythree/perl-polybar-weather/discussions/1)
and we'll do our best to help.

### Calling the notification launcher
You can call the `notifier-launcher` directly by executing:
```
~/.config/polybar/perl-polybar-weather/notification-launcher
```
For grins, you might add a keyboard `bindsym` event in i3, something like:
```
bindsym --release [your+key+combo] exec --no-startup-id ~/.config/polybar/perl-polybar-weather/notification-launcher
```

### Switching from Dunst to xfce4-notifyd under Arch (Manjaro)
If you're using dunst for notifications, the forecast mode output will not be great. We recommend using xfce4-notifyd as your i3 
notification service agent. If you're running Manjaro, the xfce4-notifyd service is already installed, but dunst takes the
org.freedesktop.Notifications spot before xfce4-notifyd can spin-up. Under Manjaro, you can't [easily] remove dunst, but you can 
disable dunst with the following:
```
sudo mv /usr/share/dbus-1/services/org.knopwob.dunst.service{,.disabled}
```
Either reboot or restart your logon session for the changes to take effect.

### Tweaks to xfce4-notifyd
If you're using `xfce4-notifyd`, you may want to include font information and some additional formatting using the `gtk.css` configuration.
This is usually found in `~/.config/gtk-3.x` (where 'x' is your GTK version). Here's a configuration example for the `xfce4-notifyd` pop-up
window (as in the image above). Remove any color declarations to follow your Xorg, GTK, or XFCE theming.

```css
#XfceNotifyWindow {
  border-radius: 4px;
  border-width: 1px;
  border-color : #488001;
  font-family: "Roboto Mono";
  font-size: 15px;
}

#XfceNotifyWindow label#summary {
  font-weight: Bold;
  border-bottom: 1px solid #488001;
  padding-bottom: 5px;
  margin-bottom: 5px;
}

#XfceNotifyWindow label#body {
  padding-bottom: 10px;
  border-bottom: 1px solid #488001;
  font-family: "Roboto Mono", "Weather Icons";
  font-size: 12px;
}

#XfceNotifyWindow button {
  background: #488001;
  color: #ffffff;
}

```

### Adding i3 options for floating windows
If you'd like the notifier button links to open in floating windows, add the lines below to your i3 config.
```ini
for_window [instance="openweathermap.org"] floating enable border normal, resize set 1024 768, move position center
for_window [instance="embed.windy.com"] floating enable border normal, resize set 1024 768, move position center

```

### References
* [Polybar custom script module](https://github.com/polybar/polybar/wiki/Module:-script)
* [OpenWeather API](https://openweathermap.org/api)
* [xfce4-notifyd](https://docs.xfce.org/apps/notifyd/start)
* [notify-send](https://man.archlinux.org/man/notify-send.1.en)

<!--  vim: set ts=4 sw=4 tw=0 et : -->
