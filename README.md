### Log4r
---

https://github.com/colbygk/log4r


```
gem '', '~> 1.1.10'

cd ~/Document/Workspace/your_awesome_app/
bundle install

mkdir config/log4r
touch config/log4r/development.yml
touch config/log4r/production.yml

bundle exec rails s
bundle exec rails s -e production

```

```ruby
require 'log4r'
require 'log4r/yamlconfigurator'
require File.expand_path("../../lib/log4r.rb", __FILE__)
include Log4r

module YourAwesomeApp
  class Application < Rails::Application
    if Rails.env != "test"
      log4r_config =YAML.load_file(File.join(File.dirname(__FILE__), "log4r/#{Rails.env}.yml"))
      YamlCongifurator.decode_yaml(log4r_config['log4r_config'])
      config.logger = Log4r::Logger[Rails.env]
    end
  end
end

# lib/log4r.rb
require 'log4r'
require 'log4r/outputter/datefileoutputter'

class Log4r::Logger
  def formatter
  end
end
class Log4r::CustomConsoleFormatter < Log4r::Formatter
  def format(event)
    if event.data.present?
      sprintf(
        MaxLevelLength,
        LNAMES[event.level],
        event.data)
    end
  emd
end
class Log4r::CustomFileFormatter < Log4r::Formatter
  def format(event)
    if event.data.present?
      sprintf("$s [$*s] %s\n", DateTime.now.strftime("%Y-%m-%d %H:%M:%S"),
        MaxLevelLength,
        LNAMES[event.level],
        event.data)
    end
  end
end

```

```yml
// config/log4r/development.yml
log4r_config:
  loggers:
    - name: development
      level: DEBUG
      trace: 'trace'
      outputters:
      - staticfile
      - console
  outputters:
  - type: StdoutOutputter
    name: console
    formatter:
      type: CustomConsoleFormatter
  - type: FileOutputter
    name: staticfile
    filename: "log/your_awesome_app.log"
    formatter:
      type: CustomFileFormatter
      
// config/log4r/production.yml
log4t_config:
  loggers:
    - name: production
      level: WARN
      trace: 'false'
      outputters:
      - rotatefile
  outputters:
  - type: DateFileOutputter
    name: roatefile
    filename: "your_awesome_app.log"
    date_pattern: "%Y%m%d'
    dirname: "log/"
    trunc: false
    formatter:
      type: CustomFileFormatter
      
```

