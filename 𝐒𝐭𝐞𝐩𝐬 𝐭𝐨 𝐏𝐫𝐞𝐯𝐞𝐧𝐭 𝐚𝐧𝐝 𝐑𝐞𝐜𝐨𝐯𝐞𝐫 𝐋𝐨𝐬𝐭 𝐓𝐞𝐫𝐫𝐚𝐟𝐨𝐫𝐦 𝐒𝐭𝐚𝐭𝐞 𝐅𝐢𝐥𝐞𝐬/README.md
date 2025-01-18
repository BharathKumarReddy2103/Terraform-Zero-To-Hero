Steps to Prevent and Recover Lost Terraform State Files (with Configuration Examples)

𝐒𝐭𝐞𝐩𝐬 𝐭𝐨 𝐏𝐫𝐞𝐯𝐞𝐧𝐭 𝐋𝐨𝐬𝐭 𝐓𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 𝐒𝐭𝐚𝐭𝐞 𝐅𝐢𝐥𝐞𝐬

𝟏. 𝐔𝐬𝐞 𝐑𝐞𝐦𝐨𝐭𝐞 𝐒𝐭𝐚𝐭𝐞 𝐒𝐭𝐨𝐫𝐚𝐠𝐞:

Store the state file remotely to ensure availability and resilience.

Example: Configure AWS S3 as the backend with DynamoDB for state locking.

Terraform Backend Configuration (AWS S3 with DynamoDB):

𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 {
𝐛𝐚𝐜𝐤𝐞𝐧𝐝 “𝐬𝟑” {
𝐛𝐮𝐜𝐤𝐞𝐭 = “𝐦𝐲-𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦-𝐬𝐭𝐚𝐭𝐞-𝐛𝐮𝐜𝐤𝐞𝐭”
𝐤𝐞𝐲 = “𝐩𝐚𝐭𝐡/𝐭𝐨/𝐦𝐲/𝐬𝐭𝐚𝐭𝐞𝐟𝐢𝐥𝐞.𝐭𝐟𝐬𝐭𝐚𝐭𝐞”
𝐫𝐞𝐠𝐢𝐨𝐧 = “𝐮𝐬-𝐞𝐚𝐬𝐭-𝟏”
𝐝𝐲𝐧𝐚𝐦𝐨𝐝𝐛_𝐭𝐚𝐛𝐥𝐞 = “𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦-𝐥𝐨𝐜𝐤-𝐭𝐚𝐛𝐥𝐞”
𝐞𝐧𝐜𝐫𝐲𝐩𝐭 = 𝐭𝐫𝐮𝐞
}
}

𝟐. 𝐄𝐧𝐚𝐛𝐥𝐞 𝐒𝐭𝐚𝐭𝐞 𝐅𝐢𝐥𝐞 𝐋𝐨𝐜𝐤𝐢𝐧𝐠:

Use DynamoDB or similar services for locking in the remote backend.

For AWS S3, create a DynamoDB table for state locking.

𝟑. 𝐄𝐧𝐚𝐛𝐥𝐞 𝐕𝐞𝐫𝐬𝐢𝐨𝐧𝐢𝐧𝐠 𝐟𝐨𝐫 𝐑𝐞𝐦𝐨𝐭𝐞 𝐁𝐚𝐜𝐤𝐞𝐧𝐝:

Turn on versioning for the S3 bucket or other remote backends to maintain a history of state file versions.

Example: AWS S3 bucket versioning can be enabled via the AWS Management Console or CLI.

𝐄𝐧𝐚𝐛𝐥𝐞 𝐕𝐞𝐫𝐬𝐢𝐨𝐧𝐢𝐧𝐠 𝐯𝐢𝐚 𝐀𝐖𝐒 𝐂𝐋𝐈:

𝐚𝐰𝐬 𝐬𝟑𝐚𝐩𝐢 𝐩𝐮𝐭-𝐛𝐮𝐜𝐤𝐞𝐭-𝐯𝐞𝐫𝐬𝐢𝐨𝐧𝐢𝐧𝐠 — 𝐛𝐮𝐜𝐤𝐞𝐭 𝐦𝐲-𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦-𝐬𝐭𝐚𝐭𝐞-𝐛𝐮𝐜𝐤𝐞𝐭 — 𝐯𝐞𝐫𝐬𝐢𝐨𝐧𝐢𝐧𝐠-𝐜𝐨𝐧𝐟𝐢𝐠𝐮𝐫𝐚𝐭𝐢𝐨𝐧 𝐒𝐭𝐚𝐭𝐮𝐬=𝐄𝐧𝐚𝐛𝐥𝐞𝐝

𝟒. 𝐄𝐧𝐜𝐫𝐲𝐩𝐭 𝐒𝐭𝐚𝐭𝐞 𝐅𝐢𝐥𝐞𝐬:

Use encryption at rest and in transit for remote storage.

Example: Enable encrypt = true in the backend configuration for S3.

𝟓. 𝐑𝐞𝐠𝐮𝐥𝐚𝐫 𝐁𝐚𝐜𝐤𝐮𝐩𝐬:

Automate backups of the state file using scripts or tools.

𝐄𝐱𝐚𝐦𝐩𝐥𝐞 𝐁𝐚𝐜𝐤𝐮𝐩 𝐒𝐜𝐫𝐢𝐩𝐭:

#!/𝐛𝐢𝐧/𝐛𝐚𝐬𝐡
𝐚𝐰𝐬 𝐬𝟑 𝐜𝐩 𝐬𝟑://𝐦𝐲-𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦-𝐬𝐭𝐚𝐭𝐞-𝐛𝐮𝐜𝐤𝐞𝐭/𝐩𝐚𝐭𝐡/𝐭𝐨/𝐬𝐭𝐚𝐭𝐞𝐟𝐢𝐥𝐞.𝐭𝐟𝐬𝐭𝐚𝐭𝐞 /𝐛𝐚𝐜𝐤𝐮𝐩𝐬/$(𝐝𝐚𝐭𝐞 +%𝐅)-𝐬𝐭𝐚𝐭𝐞𝐟𝐢𝐥𝐞.𝐭𝐟𝐬𝐭𝐚𝐭𝐞

𝟔. 𝐀𝐜𝐜𝐞𝐬𝐬 𝐂𝐨𝐧𝐭𝐫𝐨𝐥:

Use IAM roles and policies to enforce least-privilege access to the state file.

𝐄𝐱𝐚𝐦𝐩𝐥𝐞 𝐈𝐀𝐌 𝐏𝐨𝐥𝐢𝐜𝐲 𝐟𝐨𝐫 𝐒𝟑 𝐁𝐚𝐜𝐤𝐞𝐧𝐝:

{
“𝐕𝐞𝐫𝐬𝐢𝐨𝐧”: “𝟐𝟎𝟏𝟐–𝟏𝟎–𝟏𝟕”,
“𝐒𝐭𝐚𝐭𝐞𝐦𝐞𝐧𝐭”: [
{
“𝐄𝐟𝐟𝐞𝐜𝐭”: “𝐀𝐥𝐥𝐨𝐰”,
“𝐀𝐜𝐭𝐢𝐨𝐧”: [
“𝐬𝟑:𝐆𝐞𝐭𝐎𝐛𝐣𝐞𝐜𝐭”,
“𝐬𝟑:𝐏𝐮𝐭𝐎𝐛𝐣𝐞𝐜𝐭”,
“𝐝𝐲𝐧𝐚𝐦𝐨𝐝𝐛:𝐏𝐮𝐭𝐈𝐭𝐞𝐦”,
“𝐝𝐲𝐧𝐚𝐦𝐨𝐝𝐛:𝐆𝐞𝐭𝐈𝐭𝐞𝐦”
],
“𝐑𝐞𝐬𝐨𝐮𝐫𝐜𝐞”: [
“𝐚𝐫𝐧:𝐚𝐰𝐬:𝐬𝟑:::𝐦𝐲-𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦-𝐬𝐭𝐚𝐭𝐞-𝐛𝐮𝐜𝐤𝐞𝐭/*”,
“𝐚𝐫𝐧:𝐚𝐰𝐬:𝐝𝐲𝐧𝐚𝐦𝐨𝐝𝐛:𝐮𝐬-𝐞𝐚𝐬𝐭-𝟏:𝟏𝟐𝟑𝟒𝟓𝟔𝟕𝟖𝟗𝟎𝟏𝟐:𝐭𝐚𝐛𝐥𝐞/𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦-𝐥𝐨𝐜𝐤-𝐭𝐚𝐛𝐥𝐞”
]
}
]
}

𝟕. 𝐔𝐬𝐞 𝐓𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 𝐖𝐨𝐫𝐤𝐬𝐩𝐚𝐜𝐞𝐬 𝐏𝐫𝐨𝐩𝐞𝐫𝐥𝐲:

Separate environments (e.g., dev, stage, prod) into different workspaces.

𝐄𝐱𝐚𝐦𝐩𝐥𝐞 𝐂𝐨𝐦𝐦𝐚𝐧𝐝:

𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 𝐰𝐨𝐫𝐤𝐬𝐩𝐚𝐜𝐞 𝐧𝐞𝐰 𝐝𝐞𝐯
𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 𝐰𝐨𝐫𝐤𝐬𝐩𝐚𝐜𝐞 𝐧𝐞𝐰 𝐩𝐫𝐨𝐝

𝐒𝐭𝐞𝐩𝐬 𝐭𝐨 𝐑𝐞𝐜𝐨𝐯𝐞𝐫 𝐋𝐨𝐬𝐭 𝐓𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 𝐒𝐭𝐚𝐭𝐞 𝐅𝐢𝐥𝐞𝐬:

𝟏. 𝐑𝐞𝐜𝐨𝐯𝐞𝐫 𝐟𝐫𝐨𝐦 𝐑𝐞𝐦𝐨𝐭𝐞 𝐁𝐚𝐜𝐤𝐞𝐧𝐝 𝐕𝐞𝐫𝐬𝐢𝐨𝐧𝐢𝐧𝐠:

Restore a previous version of the state file.

Example: AWS S3 bucket versioning allows you to retrieve an earlier version.

𝐑𝐞𝐬𝐭𝐨𝐫𝐞 𝐚 𝐏𝐫𝐞𝐯𝐢𝐨𝐮𝐬 𝐕𝐞𝐫𝐬𝐢𝐨𝐧 𝐯𝐢𝐚 𝐀𝐖𝐒 𝐂𝐋𝐈:

𝐚𝐰𝐬 𝐬𝟑𝐚𝐩𝐢 𝐠𝐞𝐭-𝐨𝐛𝐣𝐞𝐜𝐭 — 𝐛𝐮𝐜𝐤𝐞𝐭 𝐦𝐲-𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦-𝐬𝐭𝐚𝐭𝐞-𝐛𝐮𝐜𝐤𝐞𝐭 — 𝐤𝐞𝐲 𝐩𝐚𝐭𝐡/𝐭𝐨/𝐦𝐲/𝐬𝐭𝐚𝐭𝐞𝐟𝐢𝐥𝐞.𝐭𝐟𝐬𝐭𝐚𝐭𝐞 — 𝐯𝐞𝐫𝐬𝐢𝐨𝐧-𝐢𝐝 <𝐯𝐞𝐫𝐬𝐢𝐨𝐧-𝐢𝐝> 𝐫𝐞𝐬𝐭𝐨𝐫𝐞𝐝.𝐭𝐟𝐬𝐭𝐚𝐭𝐞

𝟐. 𝐔𝐬𝐞 𝐓𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 𝐒𝐭𝐚𝐭𝐞 𝐁𝐚𝐜𝐤𝐮𝐩𝐬:

Replace the current state file with the automatic backup (terraform.tfstate.backup).

𝐄𝐱𝐚𝐦𝐩𝐥𝐞:

𝐦𝐯 𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦.𝐭𝐟𝐬𝐭𝐚𝐭𝐞.𝐛𝐚𝐜𝐤𝐮𝐩 𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦.𝐭𝐟𝐬𝐭𝐚𝐭𝐞

𝟑. 𝐌𝐚𝐧𝐮𝐚𝐥𝐥𝐲 𝐑𝐞𝐛𝐮𝐢𝐥𝐝 𝐒𝐭𝐚𝐭𝐞 𝐰𝐢𝐭𝐡 𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 𝐢𝐦𝐩𝐨𝐫𝐭:

Import existing resources into a new state file.

𝐄𝐱𝐚𝐦𝐩𝐥𝐞 𝐂𝐨𝐦𝐦𝐚𝐧𝐝:

𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦 𝐢𝐦𝐩𝐨𝐫𝐭 𝐚𝐰𝐬_𝐢𝐧𝐬𝐭𝐚𝐧𝐜𝐞.𝐞𝐱𝐚𝐦𝐩𝐥𝐞 𝐢-𝟏𝟐𝟑𝟒𝟓𝟔𝟕𝟖𝟗𝟎𝐚𝐛𝐜𝐝𝐞𝐟

𝟒. 𝐑𝐞𝐜𝐫𝐞𝐚𝐭𝐞 𝐭𝐡𝐞 𝐒𝐭𝐚𝐭𝐞 𝐅𝐢𝐥𝐞 𝐰𝐢𝐭𝐡 𝐓𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦𝐞𝐫:

Use Terraformer to regenerate the state file and configurations.

𝐒𝐭𝐞𝐩𝐬 𝐰𝐢𝐭𝐡 𝐓𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦𝐞𝐫:

Install Terraformer:

𝐛𝐫𝐞𝐰 𝐢𝐧𝐬𝐭𝐚𝐥𝐥 𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦𝐞𝐫

Generate Terraform configurations and state:

𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦𝐞𝐫 𝐢𝐦𝐩𝐨𝐫𝐭 𝐚𝐰𝐬 — 𝐫𝐞𝐬𝐨𝐮𝐫𝐜𝐞𝐬=𝐢𝐧𝐬𝐭𝐚𝐧𝐜𝐞 — 𝐫𝐞𝐠𝐢𝐨𝐧𝐬=𝐮𝐬-𝐞𝐚𝐬𝐭-𝟏

This creates configuration files and a state file in a new directory.

𝟓. 𝐑𝐞𝐬𝐭𝐨𝐫𝐞 𝐟𝐫𝐨𝐦 𝐌𝐚𝐧𝐮𝐚𝐥 𝐁𝐚𝐜𝐤𝐮𝐩𝐬:

Use automated or manual backups to restore the state file.

Example Script for Restoring Backup:

𝐜𝐩 /𝐛𝐚𝐜𝐤𝐮𝐩𝐬/𝟐𝟎𝟐𝟓–𝟎𝟏–𝟏𝟎-𝐬𝐭𝐚𝐭𝐞𝐟𝐢𝐥𝐞.𝐭𝐟𝐬𝐭𝐚𝐭𝐞 ./𝐭𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦.𝐭𝐟𝐬𝐭𝐚𝐭𝐞

𝟔. 𝐑𝐞𝐜𝐫𝐞𝐚𝐭𝐞 𝐈𝐧𝐟𝐫𝐚𝐬𝐭𝐫𝐮𝐜𝐭𝐮𝐫𝐞 𝐚𝐬 𝐚 𝐋𝐚𝐬𝐭 𝐑𝐞𝐬𝐨𝐫𝐭:

Recreate the infrastructure using the original Terraform code.

Be cautious to avoid duplicate resource creation.

𝟕. 𝐀𝐮𝐝𝐢𝐭 𝐚𝐧𝐝 𝐒𝐭𝐫𝐞𝐧𝐠𝐭𝐡𝐞𝐧 𝐏𝐫𝐞𝐯𝐞𝐧𝐭𝐢𝐯𝐞 𝐌𝐞𝐚𝐬𝐮𝐫𝐞𝐬:

Identify the root cause of state loss and enhance preventive measures.

By following these practices, you can ensure your Terraform state files remain safe and can be recovered in case of unexpected events.
