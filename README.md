This docker wraps [OBITools (1.2.13)](https://git.metabarcoding.org/obitools/obitools/wikis/home) to make the workflows a bit more portable.

The following approach calls obitools functions from Python:

```
import docker
from docker.errors import NotFound

client = docker.from_env()

# Create or start docker container named 'obitools'.
try:
    obt = client.containers.get('obitools')
    if obt.status == 'exited':
        print('Starting container obitools.')
        obt.start()
    else:
        print('Container obitools ready.')
except NotFound:
    print('Creating container obitools.')
    obt = client.containers.run(
        image='romunov/obitools:1.2.13',
        name='obitools',
        # This maps my local wolfdata to /data in container
        volumes={'/home/romunov/wolfdata': {'bind': '/data', 'mode': 'rw'}},
        tty=True, detach=True
        )
```

To run the first command from the [wolves tutorial](https://pythonhosted.org/OBITools/wolves.html#recover-full-sequence-reads-from-forward-and-reverse-partial-reads), you would run something along the lines of

```
reads_r = "wolf_R.fastq"
reads_f = "wolf_F.fastq"
output = "wolf.fastq"
wolf = obt.exec_run(tty=True, workdir='/data', cmd=f'illuminapairedend --score-min={score_min} -r {reads_r} {reads_f} > {output}')
```

The result would be found in `/home/romunov/wolfdata`.
