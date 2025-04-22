from flask import Flask, render_template
import boto3
from botocore.exceptions import NoCredentialsError

app = Flask(__name__)

# Function to fetch EC2 instances based on region
def get_ec2_instances(region):
    ec2 = boto3.client('ec2', region_name=region)
    try:
        # Fetch EC2 instance details
        response = ec2.describe_instances()
        instances = []
        for reservation in response['Reservations']:
            for instance in reservation['Instances']:
                instance_info = {
                    'InstanceId': instance['InstanceId'],
                    'Name': next((tag['Value'] for tag in instance.get('Tags', []) if tag['Key'] == 'Name'), 'N/A'),
                    'State': instance['State']['Name'],
                    'PrivateIpAddress': instance.get('PrivateIpAddress', 'N/A'),
                    'InstanceType': instance['InstanceType'],
                    'AvailabilityZone': instance['Placement']['AvailabilityZone']
                }
                instances.append(instance_info)
        return instances
    except NoCredentialsError:
        return f"Credentials not found for AWS."
    except Exception as e:
        return str(e)

@app.route('/')
def index():
    return "Please provide a region in the URL like /mumbai or /singapore."

@app.route('/<region>')
def region_page(region):
    # Map user-friendly region names to AWS region codes
    region_mapping = {
        'mumbai': 'ap-south-1',
        'singapore': 'ap-southeast-1',
        'sydney': 'ap-southeast-2'
    }

    # Check if the region is valid
    if region in region_mapping:
        aws_region = region_mapping[region]
        instances = get_ec2_instances(aws_region)
        return render_template('index.html', instances=instances, region=region)
    else:
        return "Invalid region specified. Please use a valid region (e.g., mumbai, singapore, sydney)."

if __name__ == "__main__":
    app.run(debug=True, host='0.0.0.0')

