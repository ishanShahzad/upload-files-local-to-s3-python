# loop a folder and upload files under multiple folders
from botocore.exceptions import NoCredentialsError
import boto3
import os
from sys import argv
import mimetypes

ACCESS_KEY = '**********************'
SECRET_KEY = '*********************'

bucket_name = 'my-bucket'

local_folder = 'PATH_TO_YOUR_FOLDER'
walks = os.walk(local_folder)


def upload_to_aws(bucket, local_file, s3_folder, filename):
    """local_file, s3_folder can be paths"""
    mime_type, encoding = mimetypes.guess_type(filename)
    print(mime_type)
    s3 = boto3.client('s3', aws_access_key_id=ACCESS_KEY,
                      aws_secret_access_key=SECRET_KEY)
    print('  Uploading ' + local_file + ' as ' + bucket + '/' + s3_folder)
    try:

        s3.upload_file(local_file, bucket, '%s/%s' %
                       (s3_folder, filename), ExtraArgs={'ACL': 'public-read', 'ContentType': mime_type})

        print('  '+s3_folder + ": Upload Successful")
        print('  ---------')
        return True
    except NoCredentialsError:
        print("Credentials not available")
        return False


"""For file names"""
for source, dirs, files in walks:
    print('Directory: ' + source)
    s3_folder = 'images'
    if "thumbnails" in source:
        s3_folder = "thumbnails"

    for filename in files:

        local_file = os.path.join(source, filename)

        upload_to_aws(bucket_name, local_file, s3_folder, filename)
