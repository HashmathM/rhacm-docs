# Troubleshooting subscription is not linked to the application

An application is created and set to use a subscription the uses the `matchLabel` annotation. This issue is  specifically related to product version `1.0.0`.

## Symptom: Subscription is not linked to the application

From the application console, the application is not linked to the subscription. The application topology does not display the subscription is linked to the application.
 
## Resolving the problem: Subscription is not linked to the application

1. Find the `multicluster-operators-application` pod where the application controller is running in the `multicluster-operators-application` container. Run the following command:

   ```
   % oc get pods -n open-cluster-management|grep multicluster-operators-application
   ```

   ```
   multicluster-operators-application-5d68b77964-fppqc               
   4/4     Running    3          8d
   ```

2. Check the application controller log and find the following error that is showing continuously:

   ```
   % kubectl logs -n open-cluster-management multicluster-operators-application-5d68b77964-fppqc multicluster-operators-application
   ```

3. The error shows the `MatchExpressions` selector has invalid data, such as the following errors:

   ```
   ... Failed to list <application> : v1.LabelSelectorRequirement.Values: []string: decode slice: expect [ or n, but found ", error found in #10 byte of ...|"values":"val-app-1"|..., bigger context ...|ressions":[{"key":"app","operator":"In","values":"val-app-1"}]}}},{"apiVersion":"app.k8s.io/v1beta1"|...
   ```
4. List all applications and open each application individually:

   ```
   % oc get applications --all-namespaces 
   ```
   See example: 

   ```
   NAMESPACE                 NAME                AGE
   default                   guestbook-app       9d
   default                   mortgage-app        9d
   default                   mortgagedc          9d
   default                   val-app-1           7d1h
   default                   val-app-3           7d1h
   ns-sub-1                  nginx-app-3         7d1h
   ns-sub-1                  val-app-test-err1   7d1h
   obj-sub-ns                app-obj             8h
   ```
5. Open and check the `spec.selector.matchExpressions` field in each application. 

   The incorrect display for YAML in the following example is `val-app-1`:

   ```
   selector:
       matchExpressions:
       - key: app
         operator: In
         values: val-app-1
   ```
   The correct data is defined as an array instead of a string, as in the following example:

   ```
   selector:
       matchExpressions:
       - key: app
         operator: In
         values:
         - nginx-app-details
   ```
   This incorrect value causes errors shown in the application controller log and endless reconciliation. 

   As a result, the application controller cannot handle other correct application CRs.

After deleting and replacing invalid applications CRs, the errors are stopped and correct application CRs are processed accurately.
