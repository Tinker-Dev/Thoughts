# Data Merge

```
MERGE dbo.ActiveDirectoryContent AS target
USING
		(
			SELECT		samaccountname
						,displayname
						,cn
						,title
						,department
						,Manager
						,company
						,Email
						,Phone
						,Active
						,Created
						,LastChangeOnAD
			FROM		#ADTemp
		) AS source (SAMAccountName, DisplayName, CN, Title, Department, Manager, Company, Email, Phone, Active, Created, LastChangeOnAD)
ON (
	target.SAMAccountName = source.SAMAccountName
		AND target.DisplayName = source.DisplayName
		AND target.CN = source.CN
		AND target.Title = source.Title
		AND target.Department = source.Department
		AND target.Manager = source.Manager
		AND target.Company = source.Company
		AND target.Email = source.Email
		AND target.Phone = source.Phone
		AND target.Active = source.Active
		AND target.Created = source.Created
		AND target.LastChangeOnAD = source.LastChangeOnAD
	)
WHEN MATCHED THEN
		UPDATE SET	target.SAMAccountName = source.SAMAccountName
					,target.DisplayName = source.DisplayName
					,target.CN = source.CN
					,target.Title = source.Title
					,target.Department = source.Department
					,target.Manager = source.Manager
					,target.Company = source.Company
					,target.Email = source.Email
					,target.Phone = source.Phone
					,target.Active = source.Active
					,target.Created = source.Created
					,target.LastChangeOnAD = source.LastChangeOnAD
WHEN NOT MATCHED BY TARGET THEN
		INSERT (
					SAMAccountName
					,DisplayName
					,CN
					,Title
					,Department
					,Manager
					,Company
					,Email
					,Phone
					,Active
					,Created
					,LastChangeOnAD
				)
		VALUES	(
					SAMAccountName
					,DisplayName
					,CN
					,Title
					,Department
					,Manager
					,Company
					,Email
					,Phone
					,Active
					,Created
					,LastChangeOnAD
				)
WHEN NOT MATCHED BY SOURCE THEN
		DELETE;

```