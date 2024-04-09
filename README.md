# EndPoint Detection && Response

I'll be Using Lima Charlie and Sliver as a Command and Control (C2) server to attack a Windows 11 machine. Leveraging these tools for remote access and control. Lima Charlie will provide me the opportunity for monitoring and managing endpoints, and post-exploitation actions on compromised systems. By setting up Lima Charlie as the C2 infrastructure and utilizing Sliver payloads, attackers can execute commands, extract sensitive information, deploy additional malware, and maintain persistence on the Windows 11 target. This approach enables adversaries to conduct various malicious activities, such as data theft, espionage, or system manipulation, while evading detection and maintaining control over the compromised machine. This is a hands-on project testing my competancy of analyzing logs and finding Evil


| Virtual Machine                                            | Associated Role                                    |
| ----------------------------------------------- | ----------------------------------------------------- |
| Ubuntu w/ LimaCharlie && Sliver                          |         <a href="https://github.com/BenjaminBurton/Active-Directory-Project/blob/main/src/kali-linux/README.md">SOC Analyst</a>|


```js
- Ubuntu
- LimaCharlie (SecOps)
- Sliver (c2 Server)
- Windows 11 (Attack Machine)

`The Documentation for each machine will be in src folder`
```