# proguard-scripts
Information on automatically feeding maven with proguard output

First, run proguard ignoring warnings and without obfuscation:
```
java -jar ../lib/proguard.jar -dontwarn -dontobfuscate @ele.pro 1>log1 2>log2
```

Then, unpacked original an resulting jars into respective directories:
```
mkdir Eorig
cd Eorig
unzip ../ele.jar
cd ..
mkdir Eout
cd Eout
unzip ../ele-out.jar
cd ..
```

Then list differencies between directories:
```
diff --brief -r Eorig Eout >difs
```

Filter out your classes and file differencies:
```
cat difs|grep -v warheim|grep -v Files >dif0 #warheim is the name of all my packages, substitue with yours
```

Finally create input for the maven-jar-plugin:
```
for x in `cat dif0`; do w=`echo $x|colrm 1 14|sed 's/: /\//'`; echo "<exclude>$w</exclude>"; done >dif_processed
```

Review it before importing into your pom, then paste it as below:
```
            <plugin> 
                <groupId>org.apache.maven.plugins</groupId> 
                <artifactId>maven-jar-plugin</artifactId> 
                <configuration> 
                    <excludes> 
                        <exclude>pkg1/Class1.class</exclude> 
                        <exclude>pkg2/**</exclude> 
                    </excludes> 
                </configuration> 
            </plugin> 
```
