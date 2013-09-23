# Foreman::Monit

Small command-line too to export from Procfile to monit control files

## Installation

Add this line to your application's Gemfile:

    gem 'foreman-monit', github: 'capita/foreman-monit'
    or
    gem 'foreman-monit, '~> v1.0.1'

And then execute:

    $ bundle

## Usage

'foreman-monit export' outputs a monit control file for every process listed in the given Procfile. foreman-monit
has to be called from the projects root directory and outputs to /tmp/foreman-monit per default. It is most useful in deployment
via Capistrano to automate restarting of processes or changing the processes configuration/definition after or inside
your deployment routine

You have to provide --user, --env and --app to specify the user that will be running the processes, the RAILS_ENV
to use an a general application-indentifier to name control files and groups accordingly. Additional parameters
are --target, which specifies a non-default directory to store the control files in, and --procfile if your Procfile
resides outside of the directory you call foreman-monit from.

Inside your procfile, you can use PORT, PID_FILE and RAILS_ENV in your process command, e.g.

    web: bundle exec puma -p $PORT --pidfile $PID_FILE
    worker: bundle exec rake resque:work PIDFILE=$PID_FILE

Monit will fork the command in a shell for the specified user and will redirect each output to ./<target>/<app>-<process>.log

Include the newly exported control files in your monitrc (which normally resides in /etc/monit/monitrc or /etc/monit) with
a
    include /tmp/foreman-monit/*.conf
    or
    include /your/path/to/foreman-monit/*.conf

and simply reload/restart monit with /etc/init.d/monit reload (or restart)

You can start or stop the app's jobs by issuing

    monit -g <app> start
    monit -g <app> stop

or

    monit -g <app> restart

which will typically be somewhere in your 'cap:restart' definition in your Capfile ,e.g.

    monit stop -g <app>
    foreman-monit export --app <app> --user <user> --env production
    monit reload
    monit start -g <app>

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
