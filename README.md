## Code
import pcapy
from struct import unpack
import socket

class NetAnalyzer:
    def __init__(self, interface):
        self.interface = interface

    def analyze_packet(self, header, data):
        eth_length = 14
        eth_header = data[:eth_length]
        eth = unpack('!6s6sH', eth_header)
        eth_protocol = socket.ntohs(eth[2])

        if eth_protocol == 8:  # IPv4
            ip_header = data[eth_length:20+eth_length]
            iph = unpack('!BBHHHBBH4s4s', ip_header)
            version_ihl = iph[0]
            ihl = version_ihl & 0xF
            iph_length = ihl * 4

            src_ip = socket.inet_ntoa(iph[8])
            dest_ip = socket.inet_ntoa(iph[9])

            print('Source IP:', src_ip)
            print('Destination IP:', dest_ip)

            tcp_header = data[iph_length+eth_length:iph_length+eth_length+20]
            tcph = unpack('!HHLLBBHHH', tcp_header)

            src_port = tcph[0]
            dest_port = tcph[1]
            sequence = tcph[2]
            acknowledgement = tcph[3]

            print('Source Port:', src_port)
            print('Destination Port:', dest_port)
            print('Sequence:', sequence)
            print('Acknowledgement:', acknowledgement)
            print('-------------------------')

    def start_capture(self):
        cap = pcapy.open_live(self.interface, 65536, 1, 0)
        print(f"Listening on {self.interface}...")
        while True:
            (header, data) = cap.next()
            self.analyze_packet(header, data)

if __name__ == "__main__":
    analyzer = NetAnalyzer('eth0')
    analyzer.start_capture()
    
# NetInspect
NetInspect is a lightweight Python-based network traffic analyzer tool for real-time packet capture and analysis. It provides insights into network behavior, troubleshoots issues, and enhances security.

## Features

- **Real-time Packet Capture:** Monitor network packets live.
- **Protocol Support:** Analyze Ethernet, IPv4, and TCP protocols.
- **Customizable Filters:** Focus on specific network traffic.
- **Easy-to-use Interface:** Simple command-line tool.

  ## Usage
  Once installed, you can use NetInspect from the command line. Here are some examples of common usage:
  * Capture Packets on a Specific Interface:
  netinspect --interface eth0
  * Filter Packets by Source IP Address:
  netinspect --filter src_ip=192.168.1.1

For more options and usage instructions, you can use the --help flag:
netinspect --help

## Contributing
We welcome contributions from the community! If you encounter any bugs, have feature requests, or would like to contribute code, please feel free to submit a pull request or open an issue on GitHub.

## License
NetInspect is released under the MIT License.

  ## Contact
  For questions, feedback, or suggestions, you can reach out to us at our email - kidoflinuxfamily@gmail.com .

## Installation

To install NetInspect, you need to have Python 3.x and the pcapy library installed on your system. You can install NetInspect via pip:

```bash
pip install netinspect

