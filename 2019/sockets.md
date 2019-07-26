Socket to Me: Where do Sockets Live in Go?
Gabbi Fisher



    File Descriptors

    Sockets are FDs
        - Stream
        - Datagram


Stream Sockets: C Syscalls

    Server Alice, Client Bob

    Server                      Client
    ---                         ---

    socket()

    bind() to IP:port

    listen()
        "turns phone on"

    accept()
        "waits for a call"

                                socket()

                                connect()

    read()                      write()

    write()                     read()

    close()                     close()


    8 syscalls total.


How about in Go?

    TCP

    ln, _ := net.Listen("tcp", ":8080")

    for {
        conn, err := ln.Accept()
        go handle(conn)
    }


    handle()

        conn.Read()
        conn.Write()
        ...
        conn.Close()


Datagram Sockets: UDP

    Does not guarantee delivery or order.

    Server                      Client
    ---                         ---

    socket()

    bind() to IP:port

                                socket()

    sendto()                    recvfrom()

    ...


How about Go?

    pc, err := net.ListenPacket("udp", ":8080")

    ...

    size, addr, err := pc.ReadFrom(buf)

    ...

    pc.WriteTo(..., addr)


Practical Socket Applications

    For example DNS

    Is it valid to run two sockets on one address?

    5-tuples for socket matching

        Protocol
        dstip
        dstport
        srcip
        srcport

    So yes, you can have two protocols on the same address.


Collisions

    SO_REUSEADDR

    Matches the "most specific" socket.


Setting Options in Go

    ListenPacket()
        |
        v
    ListenConfig.Control
        |
        v
    RawConfig.Control
        |
        v
    Callback passed to set socket options

    syscall.Setsockoptint(
        fd,
        protocol level,
        option_flag, 1
    )


Isn't syscall frozen/deprecated?

    Favor x/sys/unix where possible.
    Makes the specific os explicit.


