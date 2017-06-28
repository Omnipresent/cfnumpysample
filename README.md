## This app demonstrates error with satisfying `numpy` dependency in PCF

This app includes a vendor folder that has the numpy dependency downloaded. When deploying it to PCF, it should resolve the dependency from the `vendor` folder. However, it can't and throws an error. 

The dependency in the vendor folder was downloaded via the CF docker image:


```
docker run -v $PWD:/myapp -it cloudfoundry/cflinuxfs2 bash

wget https://bootstrap.pypa.io/get-pip.py

python get-pip.py

cd /myapp

pip install --download vendor -r requirements.txt

```

## Re-create

Follow the below steps to push the application:

```
cf push cfnumpysample -b python_buildpack
```

You will see an error like this:

```
Downloaded [file:///tmp/buildpack/dependencies/https___buildpacks.cloudfoundry.org_concourse-binaries_python_python-2.7.12-linux-x64.tgz]
     $ pip install -r requirements.txt
       Ignoring indexes: https://pypi.python.org/simple
       Collecting numpy (from -r requirements.txt (line 1))
  Could not find a version that satisfies the requirement numpy (from -r requirements.txt (line 1)) (from versions: )
No matching distribution found for numpy (from -r requirements.txt (line 1))
Failed to compile droplet
Exit status 223
Staging failed: Exited with status 223
Destroying container
Successfully destroyed container
```