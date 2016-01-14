#!/usr/local/bin/node

var program = require('commander')
var mpd = require('mpd')
var path = require('path')
var cmd = mpd.cmd

program
    .version(require('./package.json').version)
    .option('-h, --host [host]', 'mpd host', 'musicbox.local')
    .option('-p, --port [port]', 'mpd port', 6600)
    .option('-f, --format [format]')
    .parse(process.argv)

var client = mpd.connect({
	port: program.port,
	host: program.host
})

client.on('ready', () => {  
    client.sendCommand(cmd('currentsong', []), (err, data) => {
        if (err) {
            process.exit(-1)
        }
        else {
            var obj = {}
            var lines = data.split(/\n/)
            for (var i in lines) {
                if (lines[i].indexOf(':') > -1) {
                    var line = lines[i].split(/: /)
                    var key = line[0]
                    var val = line[1]
                    obj[key] = val
                }
            }
            
            try {
                if ( ! program.format) {
                    process.stdout.write(new Buffer(JSON.stringify(obj)))
                }
                else {
                    if (program.format.length > 0) {
                        // do some replacements in the format
                        var output = program.format
                            .replace(/%a/gi, obj.Artist)
                            .replace(/%l/gi, obj.Album)
                            .replace(/%t/gi, obj.Title)
                            .replace(/%n/gi, obj.Track)
                            .replace(/%T/gi, obj.Time)
                            .replace(/%d/gi, obj.Date)
                        process.stdout.write(new Buffer(output))
                    }
                    else {
                        console.log('')
                        console.log('  Usage: %s -f [format]', path.basename(process.argv[1], '.js'))
                        console.log('')
                        console.log('  Formats:')
                        console.log('')
                        console.log('     %a - Artist')
                        console.log('     %l - Album')
                        console.log('     %t - Title')
                        console.log('     %n - Track number')
                        console.log('     %T - Time')
                        console.log('     %d - Date')
                        console.log('')
                        
                        process.exit(-1)
                    }
                }
                
                process.exit(0)
            }
            catch (ex) {
                console.log(ex)
                process.exit(-1)
            }
        }
    })
})

client.on('error', (err) => {
    console.log('could not connect to musicbox. are you at work?')
    process.exit(-1)
})