# NGFW-rebooter
Script to reboot PANW NGFWs

====================================================Script text below=====================================================================
import requests
from requests.packages.urllib3.exceptions import InsecureRequestWarning

# Suppress only the single InsecureRequestWarning from urllib3 needed
requests.packages.urllib3.disable_warnings(InsecureRequestWarning)

# Paths to the text files
credentials_file_path = 'credentials.txt'  # Each line: API_KEY
firewall_ips_file_path = 'firewall_ips.txt'  # Each line: FIREWALL_IP

# Function to read lines from a file
def read_lines_from_file(file_path):
    with open(file_path, 'r') as file:
        return [line.strip() for line in file.readlines()]

# Function to send reboot command
def send_reboot_command(api_key, firewall_ip):
    reboot_url = f'https://{firewall_ip}/api/?type=op&cmd=<request><restart><system></system></restart></request>&key={api_key}'
    try:
        response = requests.get(reboot_url, verify=False)
        if response.status_code == 200:
            print(f"Reboot command sent successfully to {firewall_ip}.")
        else:
            print(f"Failed to send reboot command to {firewall_ip}. Status Code: {response.status_code}")
    except Exception as e:
        print(f"An error occurred when sending reboot command to {firewall_ip}: {e}")

# Read credentials and firewall IPs from files
api_keys = read_lines_from_file(credentials_file_path)
firewall_ips = read_lines_from_file(firewall_ips_file_path)

# Send the reboot command to each firewall IP using each API key
for api_key in api_keys:
    for firewall_ip in firewall_ips:
        send_reboot_command(api_key, firewall_ip)
