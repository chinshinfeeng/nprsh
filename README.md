# nprsh
an efficient ssh toolkit
一个高效的工具，可以实现并行ssh连接到多台服务器进行操作，极大地提升效率。

##USAGE:
###nprsh [-sN] [-l] [-i] [-c|-C] [-T] [-rackprefix PREFIX -rackscale M.N]
        [-on NODE_LIST] [-f NODE_LIST_FILE]
        [-F NODE_TABLE [add |remove |-q QUERY_STRING] ]
        [-e EXCLUDED_NODE_LIST] [-x EXCLUDED_NODE_LIST_FILE]
        [-E EXCLUDED_NODE_TABLE [-q QUERY_STRING] ]
        <COMMAND |-b BATCH_FILE |-np NUM [-t] MPIAPP |-up |-L |-mpiv>
###TARGET SPECIFICATION:
    -rackprefix PREFIX -rackscale M.N
          Specify the valid node range, {PREFIX}{*}M..N.
          PREFIX defaults to "gs"
          Background: It is very common that nodes are not continually
                      numbered in a cluster. The NEXT node of gs0149
                      may be gs0210, instead of gs0150. You can specify
                      "-rackscale" as 10.49 to weed out gs0150..0209
                      from gs0110..0249.
    -on NODE_LIST
          Specify target nodes. NODE_LIST may be in the form of:
            1. HOSTX
            2. HOSTX..Y
            3. G[ABC]HOSTX
            4. G[ABC]HOSTX..Y
            5. NODE_LIST,NODE_LIST
            6. IP lists, such as 10.10.11.1-108
            7. HOSTX..Y/N, the step is N
          If omitted, get target nodes from ~/.nprsh_nodes
          CAUTION: Valid characters in a node name are [A-Za-z0-9_-]
    -f NODE_LIST_FILE
          Specify NODE_LIST in a file.
    -F NODE_TABLE
          Specify NODE_TABLE to: 1. fetch nodes (also use -L), 2. update the table,
          You can use "-q" to add a database query string.
    -d "ND1D2"
          Specify that hostnames are extracted from the Nth field delimited by D1 and D2.
    -e EXCLUDED_NODE_LIST
          Nodes in EXCLUDED_NODE_LIST will not be used.
          Attention: Always get excluded nodes from ~/.nprsh_badnodes
    -x EXCLUDED_NODE_LIST_FILE
          Specify nodes that should be excluded.
    -E EXCLUDED_NODE_TABLE
          Specify EXCLUDED_NODE_TABLE. Query string WILL BE supported.
    -g PATTERN
          Specify the pattern to select dest nodes.
          Hint: You can specify two or more patterns using "\|"
    -p PREFIX
          Specify the pattern to add before each node name.
    -I
          Ignore the list of badnodes.
    -X
          Equivalent to "-x /tmp/nprsh_chenzhenfeng.dnlst".
FUNCTIONS:
    -A USER:PASSWD
           Execute as USER:PASSWD on target nodes.
    -b BATCH_FILE
           Execute COMMANDs listed in BATCH_FILE in BATCH mode.
    -sN    Run <COMMAND> on the target-nodes one by one, with N seconds
           of interval (at most 300 seconds).
    -l     Run <COMMAND> locally. Do not use in BATCH mode.
           In this mode, %DD will be substituted by a hostname!
    -L     List all the target hostname and EXIT.
    -i     Interactive mode.
    -D DT_DEGREE
           File distribution mode.
    -S STRIDE
           Stride in pairtest mode (-net).
    -U USER GROUP PASSWD
           Create USER in GROUP in batch mode.
    -P USER GROUP
           Make ssh pass-free on a list of hosts.
    -np NUM [-t] MPIAPP
           Run MPIAPP on <NUM> cores.
           "-t", generate config files from a four-var template.
    -up    Check up/down of each node.
    -rst   Hard-reset a list of nodes (USE WITH CAUTION!!!).
    -kai   Power ON a list of nodes (USE WITH CAUTION!!!).
    -guan  Power OFF a list of nodes (USE WITH CAUTION!!!).
    -load  Check the load of each node/cpu.
    -push  Distribute and Run a script or a binary executable in the dest node(s).
    -net CMD ARGS
           Run pairtest between each pair of dest nodes.
    --install
           Local installation of nprsh for current user.
    -import user comments
           Create a host table, with a name in the style of 20100507_122503_hzg
    -kick reason_code NODE_LIST
           Put a list of nodes into in_repair state, the $default_htable, which
           is defined in NPRSHRC or using "-F", will be updated accordingly.
    -pull
           Reclaim a list of hosts after repair.
    -update
           Update the version of all available hosts in a host table.
    -checkt
           Check the current state of a list of hosts.
    -listtable USER [START [STOP]]
           List host tables created by USER, after START, and before STOP.
    -set field value
           Set the specified property of a list of host.
MISC:
    -c     Color off.
    -C     Color on.
    -T     Print the timestamp of each line.
    -q     Be quiet.
    -V     Be verbose/verboser.
CAUTION:
    In parallel mode (default), the output from each target nodes
    will be messed up randomly.
EXAMPLES:
    nprsh -on g[abc]node004..128 "hostname >/etc/HOSTNAME"
init
    nprsh -on anode004..128 -e anode007,anode098 /etc/init.d/sshd start
    nprsh -f oldnodes -x badnodes -f newnodes date
    nprsh -on a118,a119,a130 -U ct test ct123
    nprsh -on m1..32 -up
    nprsh -g a1\|c19 -L
    nprsh -on [abc]110..159 -t -np 64 xhpl
    nprsh -on a110..159 scp /tmp/netdiag.log node1:/tmp/netdiag_%DD.log
    nprsh -A foo:foo123 CMD
    nprsh -on m1..32 -X -l scp nprsh.pl @@:/usr/local/bin/nprsh
    nprsh -on m19,m28 -c "dmesg |tail" |vi -
    nprsh -on m1..32 -X -l scp @@:/etc/rc.d/rc.local  rc.local_@@
    nprsh -on m1..32 -X -l scp slave_etc_hosts.@@ @@:/slave/etc/hosts
    nprsh -on gh10..89 -X -l ssh %DD ifconfig ib0 %DD/g/i/ up
