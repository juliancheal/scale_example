# Mulitple Options idea for settings

## Description

The idea behind the mulitple_option setting was to use the curent settings save
file of comma separated text values. To do this we store a - (dash) separated string
of values for example: `0-1-2`.

An example of the `settings-debug.txt`
`multiple_option:0-1-2,sfx:false,music:true,fullscreen:false`

After converting that string to a Ruby array, we are able to rotate the array on each
selection of the menu item.

First it would be `0-1-2`, when selected a second time it becomes `1-2-0`

Looking up those integer values in a method gives us the string text for the menu item.

## Code

In `app/scenes/settings.rb` first we initalise
the current value for the mulitple_option

```ruby
  if first_scene_tick?(args)
    args.state.multiple_option = args.state.setting.multiple_option.split("-")
    @multiple_option = args.state.multiple_option
  end
```

Code for the menu item.

```ruby
  {
    key: :multiple_option,
    kind: :multiple_option,
    setting_val: args.state.setting.multiple_option,
    on_select: -> (args) do
      @multiple_option = @multiple_option.rotate
      GameSetting.save_after(args) do |args|
        args.state.setting.multiple_option = @multiple_option.join("-")
      end
    end
  },
```

In `app/menu.rb` we add the new menu kind we added in `app/scenes/settings.rb`

```ruby
  text = case option.kind
          when :toggle
            "#{text(option[:key])}: #{text_for_setting_val(option[:setting_val])}"
          when :multiple_option
          "#{text(option[:key])}: #{text_for_setting_multiple_val(option[:setting_val])}"
          else
            text(option[:key])
          end
```

This is the method for `text_for_setting_multiple_val`

```ruby
  def text_for_setting_multiple_val(val)
    case val
    when "0-1-2"
      text(:option_1)
    when "1-2-0"
      text(:option_2)
    when "2-0-1"
      text(:option_3)
    else
      text(:option_1)
    end
  end
```

Text constants added to `app/text.rb`

```
  multiple_option: "Multiple Option",
  option_1: "Option 1",
  option_2: "Option 2",
  option_3: "Option 3"
```

If there is anything missing it should all be in the code.