# File Formats Reference

MetaFold works with various file formats for different purposes - from storing templates and metadata to integrating with laboratory systems. This reference documents all supported file formats, their structure, and usage patterns.

## Template Files

### Template JSON Format (.json)

Templates are stored as JSON files containing the complete template definition including folder structure, metadata schema, and configuration options.

**File Extension**: `.json`  
**Encoding**: UTF-8  
**Location**: User Templates directory or application bundle

#### Basic Structure

```json
{
  "id": "unique-template-id",
  "name": "Template Display Name",
  "description": "Template description for users",
  "type": "experiment",
  "version": "1.0.0",
  "author": "Template Author",
  "created": "2025-06-13T10:00:00.000Z",
  "updated": "2025-06-13T10:00:00.000Z",
  "folderStructure": [],
  "metadataSchema": {},
  "config": {}
}
```

#### Folder Structure Format

```json
{
  "folderStructure": [
    {
      "name": "data",
      "type": "folder",
      "description": "Raw experimental data"
    },
    {
      "name": "analysis",
      "type": "folder", 
      "description": "Data analysis and processing"
    },
    {
      "name": "README.md",
      "type": "file",
      "content": "# Project Overview\n\nProject documentation..."
    }
  ]
}
```

#### Metadata Schema Format

```json
{
  "metadataSchema": {
    "title": {
      "type": "text",
      "label": "Experiment Title",
      "required": true,
      "description": "Brief title describing the experiment"
    },
    "date": {
      "type": "date",
      "label": "Experiment Date", 
      "required": true,
      "default": "today"
    },
    "investigator": {
      "type": "text",
      "label": "Principal Investigator",
      "required": false
    },
    "temperature": {
      "type": "number",
      "label": "Temperature (°C)",
      "required": false,
      "min": -273.15,
      "max": 1000
    }
  }
}
```

#### Template Configuration

```json
{
  "config": {
    "elabFTW": {
      "enabled": true,
      "category": "experiment",
      "defaultTags": ["MetaFold", "automated"]
    },
    "omero": {
      "enabled": false,
      "datasetNamePattern": "{title}_{date}"
    },
    "validation": {
      "strictMode": false,
      "requiredFields": ["title", "date"]
    }
  }
}
```

### Template Export/Import

**Export Format**: Single JSON file containing complete template  
**Import Sources**: Local files, URLs, or template libraries  
**Validation**: Schema validation on import to ensure compatibility

## Project Metadata Files

### Primary Metadata File

**Filename**: `metadata.json` or `elabftw-metadata.json`  
**Location**: Project root directory  
**Purpose**: Stores all experiment metadata and system information

#### Structure

```json
{
  "metaFold": {
    "version": "1.1.0",
    "templateId": "basic-experiment-v1",
    "templateName": "Basic Experiment",
    "created": "2025-06-13T10:00:00.000Z",
    "projectPath": "/path/to/project",
    "integrations": {
      "elabFTW": {
        "enabled": true,
        "experimentId": 123,
        "url": "https://elab.example.com/experiments.php?mode=view&id=123"
      },
      "omero": {
        "enabled": false
      }
    }
  },
  "experiment": {
    "title": "Sample Analysis Experiment",
    "date": "2025-06-13",
    "investigator": "Dr. Smith",
    "description": "Analysis of protein samples using XYZ method"
  }
}
```

### Backup Metadata Files

**Filename Pattern**: `metadata_backup_YYYYMMDD_HHMMSS.json`  
**Purpose**: Automatic backups before metadata updates  
**Retention**: Configurable (default: 10 most recent backups)

## Integration Export Formats

### elabFTW Integration

#### Experiment Creation Payload

```json
{
  "title": "Experiment Title",
  "body": "<h2>Experiment Details</h2><p>Generated by MetaFold...</p>",
  "category_id": 1,
  "tags": ["MetaFold", "automated", "experiment"],
  "metadata": {
    "MetaFold_Version": "1.1.0",
    "MetaFold_Template": "basic-experiment-v1",
    "MetaFold_Created": "2025-06-13T10:00:00.000Z"
  }
}
```

#### Attachment Upload

**Supported Formats**: All file types supported by elabFTW  
**Upload Method**: Multipart form data  
**Metadata**: Included in attachment descriptions

### OMERO Integration

#### Dataset Creation

```json
{
  "name": "Experiment_20250613",
  "description": "MetaFold project: Sample Analysis\n\nCreated: 2025-06-13\nIntegration: MetaFold v1.1.0",
  "map_pairs": [
    ["MetaFold_TemplateType", "Experiment"],
    ["MetaFold_Created", "2025-06-13T10:00:00.000Z"],
    ["NFDI4BioImage_Tool", "MetaFold"],
    ["experiment_title", "Sample Analysis Experiment"],
    ["investigator", "Dr. Smith"]
  ]
}
```

#### Image Upload

**Supported Formats**: TIFF, PNG, JPEG, proprietary microscopy formats  
**Metadata**: Embedded in OMERO annotations and key-value pairs  
**Organization**: Images organized into datasets matching project structure

## Configuration Files

### Application Settings

**Filename**: `settings.json`  
**Location**: User data directory  
**Purpose**: Application configuration and user preferences

```json
{
  "app": {
    "theme": "light",
    "language": "en",
    "autoSave": true,
    "backupEnabled": true
  },
  "integrations": {
    "elabFTW": {
      "defaultUrl": "https://elab.example.com",
      "defaultCategory": 1,
      "autoExport": false
    },
    "omero": {
      "defaultHost": "omero.example.com",
      "defaultPort": 4064,
      "autoUpload": false
    }
  },
  "security": {
    "encryptCredentials": true,
    "sessionTimeout": 3600,
    "requireAuth": false
  }
}
```

### User Credentials (Encrypted)

**Filename**: `credentials.enc`  
**Location**: User data directory  
**Encryption**: AES-256-GCM  
**Purpose**: Securely store API keys and authentication tokens

## Data Exchange Formats

### Project Discovery Export

#### JSON Format

```json
{
  "scan": {
    "timestamp": "2025-06-13T10:00:00.000Z",
    "path": "/research/projects",
    "totalProjects": 42,
    "metaFoldProjects": 28
  },
  "projects": [
    {
      "name": "Sample Analysis",
      "path": "/research/projects/sample_analysis",
      "type": "experiment",
      "template": "basic-experiment-v1",
      "created": "2025-06-13T10:00:00.000Z",
      "lastModified": "2025-06-13T12:00:00.000Z",
      "size": "1.2 GB",
      "fileCount": 156,
      "integrations": ["elabFTW"],
      "metadata": {
        "title": "Sample Analysis Experiment",
        "investigator": "Dr. Smith"
      }
    }
  ]
}
```

#### CSV Format

```csv
Name,Path,Type,Template,Created,Size,Files,Integrations,Title,Investigator
Sample Analysis,/research/projects/sample_analysis,experiment,basic-experiment-v1,2025-06-13,1.2 GB,156,elabFTW,Sample Analysis Experiment,Dr. Smith
```

### Data Visualization Export

#### Network Graph (JSON)

```json
{
  "nodes": [
    {
      "id": "project_1",
      "label": "Sample Analysis",
      "type": "project",
      "template": "basic-experiment-v1",
      "size": 10
    }
  ],
  "edges": [
    {
      "from": "project_1",
      "to": "template_1",
      "type": "uses_template"
    }
  ]
}
```

#### Chart Data (JSON)

```json
{
  "labels": ["Experiments", "Data Collections", "Analysis Projects"],
  "datasets": [
    {
      "label": "Project Types",
      "data": [15, 8, 5],
      "backgroundColor": ["#3498db", "#2ecc71", "#e74c3c"]
    }
  ]
}
```

## Import/Export Utilities

### Batch Template Import

**Input**: ZIP file containing multiple template JSON files  
**Process**: Validates and imports all templates  
**Output**: Import report with success/failure status

### Project Archive

**Format**: ZIP archive  
**Contents**: Complete project structure + metadata  
**Use Case**: Project backup, sharing, migration

```
project_archive.zip
├── metadata.json
├── data/
│   ├── raw_data.csv
│   └── measurements.xlsx
├── analysis/
│   └── results.pdf
└── README.md
```

## File Validation

### Template Validation

- **JSON Schema**: Validates structure and required fields
- **Field Types**: Ensures metadata field types are supported
- **Dependencies**: Checks for required template dependencies

### Metadata Validation

- **Type Checking**: Validates field values against schema types
- **Required Fields**: Ensures all required fields are present
- **Format Validation**: Validates dates, numbers, and other formatted fields

### Integration Validation

- **API Compatibility**: Checks export format compatibility
- **Field Mapping**: Validates field mapping for integrations
- **Size Limits**: Enforces file size limits for uploads

## Best Practices

### File Organization

- Use descriptive filenames with version numbers
- Maintain consistent directory structures
- Keep metadata files in project root directories
- Regular backups of template and configuration files

### Data Integrity

- Always validate JSON files before use
- Use UTF-8 encoding for all text files
- Include checksums for critical data files
- Version control for template files

### Performance Considerations

- Minimize JSON file size for faster loading
- Use compressed formats for large data exports
- Implement pagination for large project lists
- Cache frequently accessed metadata

## Troubleshooting File Issues

### Common Problems

**Invalid JSON Format**
- Solution: Use JSON validator, check for trailing commas and quotes

**Encoding Issues**
- Solution: Ensure all files use UTF-8 encoding

**Permission Errors**
- Solution: Check file/directory permissions, run as administrator if needed

**Large File Handling**
- Solution: Use streaming for large files, implement progress indicators

### Recovery Procedures

**Corrupted Metadata**
- Restore from automatic backups
- Recreate from template if backups unavailable

**Missing Template Files**
- Re-import from template library
- Contact support for standard templates

**Integration Export Failures**
- Check API connectivity and credentials
- Validate export format compatibility
- Retry with simplified data

## Version Compatibility

### Template Format Versions

- **v1.0**: Basic templates with simple metadata
- **v1.1**: Added integration configurations
- **v1.2**: Enhanced validation and dependencies

### Migration Tools

MetaFold includes automatic migration tools for older file formats:

- Template format upgrades
- Metadata schema updates  
- Configuration file migrations

---

*Understanding these file formats ensures smooth operation and troubleshooting of MetaFold projects.*