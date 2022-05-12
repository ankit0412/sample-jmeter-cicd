=================Shell script for job=============

#sleep 5m # Waits 5 minutes

# Making reports folder
dateAndTime=$(date +%d-%m-%Y-%H-%M-%S)
folderName=report-$dateAndTime
fileName=results-$dateAndTime
#sourceFile=results-$(date +%d-%m-%Y).csv
sourceFile=results.csv
performanceReportFolder=$folderName/performance-report-assets
userLoad=$(($threads*3))

rm -rf $folderName/$dateAndTime
mkdir $folderName

# Running tests
/home/bitnami/apache-jmeter-5.4.1/bin/jmeter -Jthreads=$threads -Jramp=$rampUp -JloopCount=$loopCount -Jenv=$env -n -t $scenario.jmx -l $sourceFile -e -o $folderName -q "${env}var.properties"

# Graphs building and data upload is done in post build task 
mkdir -p $performanceReportFolder

echo "++++++++++"
echo " "
echo " DASHBOARD LINK: http://rakuten-oasis-loadtest.s3-website-us-east-1.amazonaws.com/$folderName"
echo " "
echo "++++++++++"

#Report Link
reportLinkPlain=http://rakuten-oasis-loadtest.s3-website-us-east-1.amazonaws.com
reportLink=http://rakuten-oasis-loadtest.s3-website-us-east-1.amazonaws.com/$folderName

# To generate env vars in email
if [ -f env_vars.txt ]; then
    rm env_vars.txt
fi
echo reportLink=$reportLink > env_vars.txt
echo sourceFile=$sourceFile >> env_vars.txt
echo reportLinkPlain=$reportLinkPlain >> env_vars.txt
echo performanceReportFolder=$performanceReportFolder >> env_vars.txt
echo folderName=$folderName >> env_vars.txt
echo userLoad=$userLoad >> env_vars.txt
