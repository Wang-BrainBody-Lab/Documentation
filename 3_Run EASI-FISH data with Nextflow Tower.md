- [Step 1 Log into the BioHPC server](#Step-1-Log-into-the-BioHPC-server)
  
- [Step 2 Start an interactive SCREEN session and bring the Seqera tower agent online](#Step-2-Start-a-slurm-session-and-bring-the-Seqera-tower-agent-online)
 
---
## Step 1 Log into the BioHPC server 
  Open the command prompt or terminal on your computer. 
### Command Prompt
For windows users, you can access the server through Command Prompt (cmd) directly using:
```
  ssh netID@cbsuwsun.biohpc.cornell.edu
```

For MacOS and Linux users, you can access the server through the terminal directly using:
```
  ssh netID@cbsuwsun.biohpc.cornell.edu
```
**Note:** You must connect to Cornell campus network or use [CU VPN](https://it.cornell.edu/cuvpn#toc-who-can-us-Ikb_bdqc) to use server. Please enter your BioHPC password (may be different from your Cornell account).
                                                                     
## Step 2 Start an interactive SCREEN session and bring the Seqera tower agent online 
Once logged into your bioHPC account, run the following command: 
```
  sbatch nextflow_tower.sh
```                                                                    
This should start an interactive SCREEN session. Use `screen -ls` to verify the SCREEN session has been started. 
Use the following command to attach to this session: 
```
  screen -r
```                                                                     
Now the Seqera (Nextflow) tower agent is online! You can switch to the [seqera webpage](https://cloud.seqera.io/user/) to set up the EASI-FISH run. 
