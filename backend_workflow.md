BackEnd Photo Upload Process

``` mermaid
classDiagram
  class VueUpload{

  }
  class AwsApiGateway{

  }
  class AwsS3LambdaTriger{

  }
  class DirectUploadController{
    <<Action called by awsS3lambda -> submit>>
    submit action creates ApiSubmission instance
    ApiSubmission.
    create
    [partner: partner, 
    controller: 'Apis::DirectUploadController',
    action: 'submit',
    submission_request_id: params[:uuid], request: params[:direct_upload].to_json]
  }
  DirectUploadController --|> ApiSubmission

  class ApiSubmission{
    when ApiSubmission instance created on commit call,
    ApiSubmissionJob.perform_later[self->[instance of ApiSubmission]]
  }

  ApiSubmission --|> ApiSubmissionJob

  class ApiSubmissionJob{
    << ApiSubmissionJob.perform_later triggers perform action>>
    ApiSubmissionJob.perform later takes as parametter ApiSubmission instance
    ie def perform[submission]
    it uses conditions to know submission.controller
    if submission.controller=='Apis::DirectUploadController' && submission.action=='submit'
      submission.outcome = ApiSubmissionDirectUpload.handle_upload[submission]
  }

  VueUpload --|> AwsApiGateway

  AwsApiGateway --|> AwsS3LambdaTriger

  AwsS3LambdaTriger --|> DirectUploadController

```