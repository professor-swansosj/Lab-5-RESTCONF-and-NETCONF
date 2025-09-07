# Lab 5 RESTCONF and NETCONF

## :triangular_flag_on_post: Learning Goals
- Understand the purpose of RESTCONF and NETCONF as standard interfaces for managing and automating network devices
- Differentiate between RESTCONF (HTTP/JSON/YANG-based) and NETCONF (XML/YANG over SSH) in terms of protocols, data formats, and use cases
- Establish connections to network devices using both the Python requests library for RESTCONF and the ncclient library for NETCONF
- Perform basic operational queries (e.g., retrieve hostname, interfaces, or capabilities) via RESTCONF and NETCONF
- Practice working with structured data models (YANG) by examining how device information is exposed and consumed in JSON or XML
- Compare API workflows: RESTCONF with HTTP verbs and JSON responses vs. NETCONF with RPC messages and XML responses
- Implement basic automation scripts that query devices, parse the returned data, and present it in a human-readable way
- Generate a simple automated network report (e.g., hostname, software version, interface list) from either RESTCONF or NETCONF output
- Reinforce error handling and session management when interacting with network APIs
- Develop confidence transitioning from toy APIs (Dad Jokes, Deck of Cards) to real-world device APIs used in enterprise network automation

## :globe_with_meridians: Overview:

In this lab, you will take the step from experimenting with public APIs into the world of network device APIs using Cisco’s DevNet Always-On Sandbox. Modern network automation relies on standardized interfaces that expose device configurations and operational data in structured, machine-readable formats. Two of the most widely used protocols are RESTCONF, which builds on HTTP and JSON, and NETCONF, which uses XML over SSH. Together, they provide consistent ways to interact with devices, whether you’re pulling status information or applying configuration changes.

You’ll begin by exploring RESTCONF through the Python `requests` library, sending simple HTTP calls to retrieve device details and practice parsing JSON output. Next, you’ll work with NETCONF using the `ncclient` library, where you’ll establish a session over SSH and issue RPC calls that return XML-formatted data. By the end of the lab, you will combine what you’ve learned to build a small automation script that gathers basic information—such as hostname, software version, and interface details—and generates a formatted report. This will give you practical experience with real-world device APIs while reinforcing the importance of structured data models in network automation.

### Introduction to Device APIs
Application Programming Interfaces, or **APIs**, act as the bridge between your scripts and the systems you want to control. Instead of logging into a device manually and typing commands, an API provides a structured, programmatic way to retrieve information or apply changes. In networking, this means you can use code to gather interface statistics, check software versions, or push new configurations without touching the CLI directly.

The key benefit of device APIs is consistency. No matter which device you connect to, the API defines how data is requested, the format it comes back in, and the rules for making changes. This standardization makes automation predictable and scalable across large environments. In this lab, you’ll focus on two major network APIs—**RESTCONF** and **NETCONF**—which are built on different protocols but share the same purpose: giving you programmatic access to device data in a structured, machine-friendly way.

### What is RESTCONF?
RESTCONF is a protocol that allows you to interact with network devices using familiar HTTP methods (like GET, POST, PUT, and DELETE) while exchanging data in structured formats such as **JSON** or **XML**. It’s built on top of REST principles, meaning you work with resources identified by URLs—for example, retrieving `/restconf/data/interfaces` to get a list of interfaces from a router. Under the hood, RESTCONF uses device data models defined in YANG, which ensure the information you receive is consistent and well-structured across different devices.

The advantage of RESTCONF is that it feels very similar to other web APIs you’ve already practiced with. You send requests over HTTP(S), include headers for things like authentication and data type, and parse the JSON response just like you did with the Dad Jokes or Deck of Cards APIs. The difference is that now, instead of jokes or playing cards, the data represents real device details such as configuration, capabilities, or operational state. This makes RESTCONF an approachable entry point into the world of network automation.

### What is NETCONF?
NETCONF (Network Configuration Protocol) is a standards-based protocol designed specifically for managing and configuring network devices. Unlike RESTCONF, which uses HTTP and JSON, NETCONF operates over **SSH** and exchanges structured data in **XML** format. It’s tightly coupled with YANG models, which define the data structure and ensure that configuration and operational information is machine-readable and consistent.

A key feature of NETCONF is its use of Remote Procedure Calls (RPCs). Instead of sending simple GET or POST requests, you issue RPC operations like `<get>`, `<get-config>`, or `<edit-config>` inside an XML payload. These calls let you retrieve state data, pull running or startup configurations, and even push new configuration snippets to the device in a controlled way. Because it runs over SSH, NETCONF also inherits a strong security model, making it a trusted option for enterprise environments.

In practice, NETCONF is more structured and powerful than RESTCONF, but it can feel heavier because of the XML syntax and RPC framework. That’s why libraries like ncclient are so useful: they handle the details of the session and the XML messages, letting you focus on the actual automation logic.

### Comparing RESTCONF and NETCONF
RESTCONF and NETCONF are both standards-based protocols that serve the same purpose: providing a consistent, model-driven way to interact with network devices. Where they differ is in their transport, data formats, and style of interaction. RESTCONF is built on top of **HTTP(S)**, making it lightweight and familiar to anyone who has worked with web APIs. It typically returns data in **JSON**, which is easy to parse in Python and friendly to modern automation tools. NETCONF, on the other hand, uses SSH for its transport and exchanges XML messages wrapped in **RPCs**. This makes NETCONF more verbose but also more powerful when dealing with complex configuration changes.

From a workflow perspective, RESTCONF feels very much like the other APIs you’ve already practiced with—simple GET and POST requests to retrieve or change resources. NETCONF requires a bit more structure: you send explicit RPC calls like `<get>` or `<edit-config>`, which can feel more formal but also give you fine-grained control over configuration management. Many environments support both, and your choice often depends on the tools you’re using and the level of detail or control you need. RESTCONF is often favored for quick queries and lightweight automation, while NETCONF is used for richer configuration tasks where transaction safety and detailed feedback are critical.

### Making Your First RESTCONF Call with Python Requests
Connecting to a device with RESTCONF feels very similar to the API work you’ve already done—except now the data represents live network information. A RESTCONF call is just an **HTTP** request sent to the device’s RESTCONF endpoint, usually over HTTPS. In the Cisco DevNet Sandbox, for example, you might request something like:

```python

import requests
from requests.auth import HTTPBasicAuth

url = "https://sandbox-iosxe-latest-1.cisco.com/restconf/data/ietf-interfaces:interfaces"
headers = {"Accept": "application/yang-data+json"}

response = requests.get(url, auth=HTTPBasicAuth("developer", "C1sco12345"), headers=headers, verify=False)
print(response.status_code)
print(response.json())


```

In this example:

- The `url` points to the device’s RESTCONF resource (here, the interfaces).

- The `auth` section provides a username and password.

- The `headers` tell the device you expect JSON based on YANG data.

- The response gives you structured interface information you can parse just like other JSON.

This first call demonstrates the main pattern of RESTCONF: identify the resource you want (via a URL), choose the right HTTP method, and handle the response. Once you can make one successful request, you can expand to other resources, loop through multiple endpoints, or build automation around the data.

### Exploring JSON Output from RESTCONF
When you make a RESTCONF request, the device responds with structured data, typically formatted in **JSON**. This output is based on a YANG model, which defines the hierarchy and fields available for that resource. For example, when you query the `interfaces` resource, the response might include keys like `ietf-interfaces:interfaces`, `interface`, `name`, `type`, and `enabled`. The structure can look more complex than the Dad Jokes API, but the principle is the same—you navigate the keys and values to extract the information you need.

In Python, the `requests` library makes it straightforward to handle this data. Calling `response.json()` turns the device’s reply into a dictionary that you can traverse. For instance, you could loop through the list of interfaces and print out their names and statuses. Working with RESTCONF JSON output gives you practice in handling nested data, since real device models often include multiple levels of detail. Once you’re comfortable locating the right keys and values, you can filter, format, and present the data in any way that suits your automation task.

### Connecting to a Device with ncclient (NETCONF)
Working with NETCONF requires a bit more setup than RESTCONF because it communicates over **SSH** and uses XML for requests and responses. The Python library ncclient handles most of the heavy lifting: it establishes the SSH session, manages capabilities, and sends properly formatted RPC messages to the device. With just a few lines of code, you can connect and issue your first `<get>` request.

For example:

```python

from ncclient import manager

with manager.connect(
    host="sandbox-iosxe-latest-1.cisco.com",
    port=830,
    username="developer",
    password="C1sco12345",
    hostkey_verify=False
) as m:
    # Retrieve the device's capabilities
    for capability in m.server_capabilities:
        print(capability)

```

In this snippet, `manager.connect()` opens the NETCONF session, and the context manager (`with ... as m:`) ensures it closes cleanly when done. The `server_capabilities` list shows what the device supports, including which YANG models it can expose. From here, you can start issuing RPC calls such as `<get-config>` to pull configuration data or `<get>` to retrieve operational information. This first connection establishes the foundation for using NETCONF as a structured, secure channel for network automation.

### Exploring XML Output from NETCONF
When you issue a NETCONF RPC, the device responds with structured data formatted as XML. Unlike JSON, XML uses opening and closing tags to define elements, often nested many layers deep. For example, a `<get>` call to retrieve interface details may return a `<data>` element containing `<interfaces>` with multiple `<interface>` entries, each describing a name, type, and status. While the format can look verbose, it is highly structured and aligns with the same YANG models used in RESTCONF.

In Python, the `ncclient` library returns these responses as XML strings. You can print them directly to see the raw structure, or use an XML parser like `xmltodict` to convert them into dictionaries that feel similar to working with JSON. Understanding the XML hierarchy is important because you often need to navigate through multiple nested elements to extract the specific values you care about—like pulling only the interface names or administrative states. Practicing with NETCONF XML output teaches you how to work with structured but complex data, which is a common reality in network automation.

### Working with YANG Models
Both RESTCONF and NETCONF rely on **YANG** models to define how device data is structured and described. Think of a YANG model as a blueprint: it specifies what resources exist on a device (like interfaces, routing protocols, or system information), the names of those resources, and the types of values they hold. Because of YANG, you can ask for `interfaces` on two different devices and get back data with the same structure, even if the underlying platforms are different. This consistency is what makes model-driven automation scalable.

When you make a RESTCONF call, the JSON keys you see—such as `ietf-interfaces:interfaces`—come directly from a YANG model. With NETCONF, the XML tags in your `<get>` or `<get-config>` responses also map to YANG definitions. While you don’t need to write YANG in this lab, being able to recognize the model-driven structure helps you understand why certain fields appear and how to navigate them. Over time, knowing how to reference YANG models lets you predict what data is available, validate configurations before pushing them, and build scripts that are portable across vendors and platforms.

### Error Handling in Network API Calls
When working with network device APIs, things won’t always go smoothly. Devices may be offline, credentials may be wrong, or the request may not match the device’s supported YANG models. That’s why it’s important to add error handling to your scripts. In RESTCONF, check the HTTP status codes: `200` means success, `401` indicates an authentication issue, `404` means the resource doesn’t exist, and `500` signals a server-side error. In Python, you can use `response.ok` or wrap calls with `try/except` and response.`raise_for_status()` to catch problems early.

With NETCONF, errors are typically returned inside the XML response as `<rpc-error>` elements. These may include error messages, severity, and even which part of your request caused the problem. In Python with `ncclient`, failed operations often raise exceptions that you can handle with `try/except` blocks. Adding timeouts to both RESTCONF and NETCONF sessions also prevents your scripts from hanging indefinitely if a device is unreachable. By building in these safeguards, your automation becomes more reliable and predictable—a key trait of production-ready tools.

### Generating an Automated Device Report
The final step of this lab brings everything together: using RESTCONF or NETCONF to build a script that produces a simple, readable **device report**. The goal is to query a few key details—such as hostname, software version, and interface information—then format them into an output that could be shared with a teammate or included in documentation. With RESTCONF, you’ll pull this data through HTTP calls and parse JSON; with NETCONF, you’ll use `ncclient` to issue `<get>` or `<get-config>` RPCs and extract values from XML.

Once the data is collected, you can assemble it into a clean report. This could be as simple as printing text in the terminal:

```markdown

Device Report
-------------
Hostname: ios-xe-mgmt.cisco.com
Software Version: 17.9.1a
Interfaces:
  - GigabitEthernet1: up/up
  - Loopback0: up/up

```

The exercise demonstrates how automation moves beyond raw API calls into producing useful deliverables. By combining multiple requests, parsing structured data, and formatting it for humans, you’re practicing the same workflow that underpins network monitoring, compliance checks, and automated documentation in real-world environments.

---

## :card_file_box: File Structure:

'''
file structure
'''

---

## Components
text

### 1. **Component 1**
text

### 2. **Component 2**
text

### 3. **Component 3**
text

## :memo: Instructions
1. text
2. text
3. text

## :page_facing_up: Logging
text

## :green_checkmark: Grading Breakdown
- x pts: 
- x pts:
- x pts: