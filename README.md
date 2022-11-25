EEM Script

To Create an EEM Script

Replace items in double angle brackets "<< >>"

For uplink on Bundle-Ethernet<<XXX>>
The TCL requires Track<<XXX>> on Bundle-EthernetXXX

If Peering is to atlga001ag1 use <<PEERING_ADDRESS>> of 68.66.72.44
If Peering is to mobal001ag1 use <<PEERING_ADDRESS>> of 68.66.72.183

Save as .tcl not .txt and start at 
::cisco::eem::event_register_track <<XXX>> state any tag e1 maxrun 120

Update Tag

For First EEM to BE XXX use tag e1
For Second EEM to BE YYY use tag e2

The only references of tag is in the following line
::cisco::eem::event_register_track <<XXX>> state any tag e1 maxrun 120

In the First EEM
::cisco::eem::event_register_track <<XXX>> state any tag e1 maxrun 120

In the Second EEM
::cisco::eem::event_register_track <<YYY>> state any tag e2 maxrun 120

Naming convention
Use BEXXX.tcl where XXX is the Bundle being monitored
_________________________________________________________________

::cisco::eem::event_register_track <<XXX>> state any tag e1 maxrun 120
namespace import ::cisco::eem::*
namespace import ::cisco::lib::*

set VERSION "1.00"

array set trackinfo [event_reqinfo]
set state $trackinfo(track_state)

if [catch {cli_open} result] {
    error $result $errorInfo
} else {
    array set cli1 $result
}

if { $state == "down"} {
    action_syslog msg "EEM detected Bundle-Ether<<XXX>> is down"

    if [catch {cli_exec $cli1(fd) "enable"} result] {
            error $result $errorInfo
    }

    if [catch {cli_exec $cli1(fd) "configure"} result] {
            error $result $errorInfo
    }


    if [catch {cli_exec $cli1(fd) "router bgp 30036"} result] {
            error $result $errorInfo
    }

    if [catch {cli_exec $cli1(fd) "neighbor <<PEERING_ADDRESS> shutdown"} result] {
            error $result $errorInfo
    }

    if [catch {cli_exec $cli1(fd) "commit"} result] {
            error $result $errorInfo
    }

    if [catch {cli_exec $cli1(fd) "end"} result] {
            error $result $errorInfo
    }
}

if { $state == "up"} {
    action_syslog msg "EEM detected Bundle-Ether<<XXX>> is up"

    if [catch {cli_exec $cli1(fd) "enable"} result] {
            error $result $errorInfo
    }

    if [catch {cli_exec $cli1(fd) "configure"} result] {
            error $result $errorInfo
    }

    if [catch {cli_exec $cli1(fd) "router bgp 30036"} result] {
            error $result $errorInfo
    }

    if [catch {cli_exec $cli1(fd) "no neighbor <<PEERING_ADDRESS> shutdown"} result] {
            error $result $errorInfo
    }

    if [catch {cli_exec $cli1(fd) "commit"} result] {
            error $result $errorInfo
    }

    if [catch {cli_exec $cli1(fd) "end"} result] {
            error $result $errorInfo
    }
}

# Close open cli before exit.
if [catch {cli_close $cli1(fd) $cli1(tty_id)} result] {
    error $result $errorInfo
}

# debug message
action_syslog msg "EEM Action for BE<<XXX>> Completed"
