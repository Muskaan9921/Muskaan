set ns [new simulator]
#create trace file
set tracefile [open dos.tr.w]
$ns trace.all $tracefile
#nam file creation
set namfile [open dos.nam w]
$ns namtrace-all $namfile
#finish procedure
proc finish {} {
    global ns tracefile namfile
    $ns flush-trace
    close $tracefile
    close $namfile
    exec nam dos.nam &
    exit 0
}

set server1  [$ns node]
set server2  [$ns node]
set client1  [$ns node]
set client2  [$ns node]
set attacker  [$ns node]
# connection
$ns duplex.line $client1 $server1  12 Mb 100Ms DropTail
$ns duplex.line $client2 $server1 12 Mb 100Ms DropTail
$ns duplex.line $attacker $server1 12 Mb 100Ms DropTail
$ns duplex.line $server1  $server2 12 Mb 100Ms DropTail
$ns queue-limit $server1  $server2 20
#create agents
set udp1 [new Agent/UDP]
$ns attach-agent $client1 $udp1
set udp2 [new Agent/UDP]
$ns attach-agent $client2 $udp2
set udp3 [new Agent/UDP]
$ns attach-agent $attacker $udp3
set null [new Agent/UDP]
$ns attach-agent $server2 $null
$ns connect $udp1 $null
$ns connect $udp2 $null
$ns connect $udp3 $null
set cbr1 [new Application/Traffic/CBR]
$cbr1 attach-agent $udp1
$cbr1 set packet_size_7000
$cbr1 set rate_ 0.4Mb
$cbr1 setrandom_ false
$cbr1 set interval_ 0.08
set cbr2 [new Application/Traffic/CBR]
$cbr2 attach-agent $udp2
$cbr2 set packet_size_4000
$cbr2 set rate_ 0.6Mb
$cbr2 setrandom_ false
$cbr2 set interval_ 0.08
set cbr3 [new Application/Traffic/CBR]
$cbr3 attach-agent $udp1
$cbr3 set packet_size_7000
$cbr3 set rate_ 0.4Mb
$cbr3 setrandom_ false
$cbr3 set interval_ 0.08
