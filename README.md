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

## Resolve

The above can be resolved by manually going out to https://pypi.python.org/pypi/numpy and downloading https://pypi.python.org/packages/51/2e/4fd51aea41986fb36b8ff43f8b135fbb1ec793045f5f156cb70da7cdb1c9/numpy-1.13.0-cp27-cp27m-manylinux1_x86_64.whl#md5=f9bf8291b035084c1b044dd251f7b2f6 file and placing it in the vendor directory. 

However, question still remains why does `pip install --download vendor -r requirements.txt` download `numpy-1.13.0-cp27-cp27mu-manylinux1_x86_64.whl` (which **doesn't** work) instead of downloading `numpy-1.13.0-cp27-cp27m-manylinux1_x86_64.whl` (which **does** work). **Note** the difference in `cp27mu` vs. `cp27m`.