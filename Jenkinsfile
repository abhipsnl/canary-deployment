	node {
	     stage('Build Image') {
	         parallel 'Master' : {
	              openshift.withCluster() {
	              openshift.withProject("amexpoc") {
	              //openshift.selector("bc", "helloamex").startBuild("--wait=true")
	                        }
	         }
	         }, 'Feature Release': {
	             if (env.threshold == "100") {
	                 echo "Skipping this build"
	                 return
	             }
	             openshift.withCluster() {
	             openshift.withProject("amexpoc") {
                 //openshift.selector("bc", "next-helloamex").startBuild("--wait=true")
	                        }
	         }
	         }
	         }
	     stage('Tag Image') {
	         parallel 'Master': {
	            openshift.withCluster() {
	            openshift.withProject("amexpoc") {
	            //openshift.tag("helloamex:latest", "helloamex:${BUILD_NUMBER}")
	                }
	            }   
	         }, 'Feature release': {
	             if (env.threshold == "100") {
	                 echo "Skipping this release"
	                 return
	             }
	             openshift.withCluster() {
	             openshift.withProject("amexpoc") {
	             //openshift.tag("next-helloamex:latest", "next-helloamex:${BUILD_NUMBER}")
	                }
	             }
	         }
	         
	     }
	     stage('Rolling out the Release') {
	         
	         script {
	         def response = sh(script: 'curl $(oc get route canarytesting --template \'{{.spec.host}}\')',returnStdout: true).trim()
             echo '=========================Response===================' + response
            
             if (env.threshold == "100") {
                 def full_route = sh (script: 'oc set route-backends canarytesting helloamex=100%',returnStdout: true).trim()
                 echo '======Full Route======' + full_route
             } else {
                 total_threshold = env.threshold
                 def feature_threshold = sh(script: "expr 100 - ${total_threshold}", returnStdout: true).trim()
                 def devided_route = sh(script: "oc set route-backends canarytesting helloamex=${total_threshold}% next-helloamex=${feature_threshold}%",returnStdout: true).trim()
                 echo '===========devided_route=========' + devided_route
                    }  
	         }
	     }
	    
	}
