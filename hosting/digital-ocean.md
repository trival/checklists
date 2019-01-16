# Digital ocean

## setup swap file

See:
http://www.rubyfleebie.com/2018/12/14/how-to-use-dokku-on-digitalocean-and-deploy-rails-applications-like-a-pro/
and
https://stackoverflow.com/questions/22272339/rake-assetsprecompile-gets-killed-when-there-is-a-console-session-open-in-produ/22272491#22272491

```
sudo swapon -s
```

No swap file shown? Check how much disk space space you have:

```
df
```

To create a swap file:

Step 1: Allocate a file for swap

```
sudo fallocate -l 2048m /mnt/swap_file.swap
```

Step 2: Change permission

```
sudo chmod 600 /mnt/swap_file.swap
```

Step 3: Format the file for swapping device

```
sudo mkswap /mnt/swap_file.swap
```

Step 4: Enable the swap

```
sudo swapon /mnt/swap_file.swap
```

Step 5: Make sure the swap is mounted when you Reboot. First, open fstab

```
sudo nano /etc/fstab
```

Finally, add entry in fstab (only if it wasn’t automatically added)

```
/mnt/swap_file.swap none swap sw 0 0
```
