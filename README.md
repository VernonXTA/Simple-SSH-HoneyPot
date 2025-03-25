# SSH Honeypot with Cowrie

## Overview
This project sets up an SSH honeypot using **Cowrie**, a widely used medium-interaction honeypot. The purpose of this honeypot is to deceive malicious threat actors into interacting with a decoy server instead of the actual service, allowing for the collection of attack data, credential harvesting, and behavioral analysis.

## Features
- Logs unauthorized login attempts and commands executed by attackers.
- Captures credentials used in brute-force attempts.
- Emulates a fully interactive SSH environment.
- Supports file logging and JSON-based structured logs.
- Can be configured to forward logs to SIEM solutions for further analysis.
- Optionally integrates with threat intelligence feeds for automated blocking.

## How It Works
Cowrie operates by mimicking a real SSH server. When an attacker connects, they are given a simulated shell environment, but any commands they execute are recorded for analysis. This allows security researchers and defenders to monitor attack patterns without exposing real infrastructure.

## Installation & Setup

### Prerequisites
Ensure your system has the following:
- Ubuntu/Debian or CentOS (recommended)
- Python 3
- Git

### Installation Steps
1. **Clone the repository:**
   ```bash
   git clone https://github.com/yourusername/ssh-honeypot.git
   cd ssh-honeypot
   ```
2. **Install dependencies:**
   ```bash
   sudo apt update && sudo apt install -y python3-venv python3-pip
   ```
3. **Set up Cowrie:**
   ```bash
   git clone https://github.com/cowrie/cowrie.git
   cd cowrie
   python3 -m venv cowrie-env
   source cowrie-env/bin/activate
   pip install -r requirements.txt
   ```
4. **Configure Cowrie:**
   ```bash
   cp cowrie.cfg.dist cowrie.cfg
   nano cowrie.cfg
   ```
   - Change `listen_port` if needed (default: `2222`).
   - Configure logging and file storage as required.

5. **Start the honeypot:**
   ```bash
   bin/cowrie start
   ```

6. **Check logs for captured activity:**
   ```bash
   tail -f log/cowrie.json
   ```

## Log Analysis
Cowrie generates logs in multiple formats:
- **Plaintext logs** (`log/cowrie.log`): Contains session data.
- **JSON logs** (`log/cowrie.json`): Structured log format for ingestion into SIEMs like Splunk or ELK.
- **Session playback** (`log/tty/`): Replays attacker sessions.

Example JSON log entry:
```json
{
    "eventid": "cowrie.login.failed",
    "username": "root",
    "password": "123456",
    "timestamp": "2025-03-24T12:34:56.789Z",
    "src_ip": "192.168.1.100"
}
```

## Hardening & Security Considerations
- **Run Cowrie on a non-standard port (e.g., `2222`)** to avoid interference with real SSH services.
- **Use IPTables or firewall rules** to redirect traffic from `port 22` to Cowrie’s listening port.
- **Ensure logs are stored securely** and rotated to prevent excessive storage use.
- **Monitor logs regularly** for attack trends and potential automated threat responses.

## Future Enhancements
- Integration with **Threat Intelligence Feeds** (AbuseIPDB, AlienVault OTX, etc.)
- Automated **attack trend visualization** using ELK Stack
- **Email or Slack alerts** for specific attack patterns
- Honeypot network deception techniques for enhanced realism

## Disclaimer
This project is intended for research and educational purposes only. Deploying a honeypot in a production network should be done with caution, and collected data should be handled responsibly.

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contact
If you have questions or suggestions, feel free to reach out or open an issue in the repository.
