
# Content Analysis Orchestrator - Retry System and Analysis Documentation

## Table of Contents
1. [Retry System Overview](#retry-system-overview)
2. [Retry Action Flags](#retry-action-flags)
3. [Error Categories and Codes](#error-categories-and-codes)
4. [Payload Response Structure](#payload-response-structure)
5. [Analysis Required Flags](#analysis-required-flags)
6. [Sample Payloads](#sample-payloads)
7. [Implementation Details](#implementation-details)

## Retry System Overview

The Content Analysis Orchestrator implements a sophisticated retry system that categorizes errors and determines appropriate retry actions based on error types. The system is designed to handle different failure scenarios with appropriate retry strategies.

### Key Components
- **ErrorHandler**: Manages error categorization and retry logic
- **APIErrorHandler**: Handles API-specific errors and retry decisions
- **ContentAnalysis**: Main orchestrator that processes records and applies retry logic

## Retry Action Flags

The system uses three main retry action flags to determine how to handle different types of errors:

### 1. `retry` - Immediate Retry
- **When Used**: For transient API errors and infrastructure issues
- **Behavior**: Automatically retries the operation with exponential backoff
- **Examples**: API timeouts, rate limiting, temporary unavailability

### 2. `no_retry` - No Retry (Debug Required)
- **When Used**: For system-level errors that require investigation
- **Behavior**: Logs error and stops processing - manual intervention needed
- **Examples**: Network connection errors, Kafka producer/consumer errors, memory issues

### 3. `retry_after_correction` - Retry After Data Correction
- **When Used**: For validation errors and data corruption issues
- **Behavior**: Waits for data to be corrected upstream before retrying
- **Examples**: Missing required fields, invalid image URLs, corrupted data

## Error Categories and Codes

### Validation Errors (ERR_1000 Series) - `retry_after_correction`
```python
MISSING_REQUIRED_FIELD = "ERR_1001"
INVALID_FIELD_TYPE = "ERR_1002"
INVALID_FIELD_VALUE = "ERR_1003"
MISSING_META_INFO = "ERR_1004"
INVALID_IMAGE_URL_FORMAT = "ERR_1005"
EMPTY_PAYLOAD = "ERR_1006"
INVALID_JSON_STRUCTURE = "ERR_1007"
IMAGE_DOWNLOAD_FAILED = "ERR_1008"
```

### API Errors (ERR_2000 Series) - `retry`
```python
TEXT_API_TIMEOUT = "ERR_2001"
IMAGE_API_TIMEOUT = "ERR_2002"
TEXT_API_UNAVAILABLE = "ERR_2003"
IMAGE_API_UNAVAILABLE = "ERR_2004"
API_RATE_LIMIT_EXCEEDED = "ERR_2005"
API_AUTHENTICATION_FAILED = "ERR_2006"
API_INTERNAL_ERROR = "ERR_2007"
```

### System Errors (ERR_3000 Series) - `no_retry`
```python
NETWORK_CONNECTION_ERROR = "ERR_3001"
KAFKA_PRODUCER_ERROR = "ERR_3002"
KAFKA_CONSUMER_ERROR = "ERR_3003"
DATABASE_CONNECTION_ERROR = "ERR_3004"
MEMORY_ALLOCATION_ERROR = "ERR_3005"
GENERAL_SYSTEM_ERROR = "ERR_3006"
```

### Data Corruption Errors (ERR_4000 Series) - `retry_after_correction`
```python
CORRUPTED_IMAGE_FILE = "ERR_4001"
MALFORMED_NUTRITION_DATA = "ERR_4002"
INVALID_ENCODING = "ERR_4003"
CHECKSUM_MISMATCH = "ERR_4004"
```

## Payload Response Structure

### Valid Payload Response (Success)
When processing succeeds, the response includes:
- **processing_status**: "SUCCESS"
- **analysis**: Array of analysis results
- **timing information**: text_analysis_time, image_analysis_time, total_time_taken
- **final scores**: score, score_range
- **audit information**: audit_timestamp, kafka_partition

### Error Payload Response (Failure)
When processing fails, the response includes:
- **processing_status**: "FAILED"
- **error_details**: Comprehensive error information
  - error_code: Specific error code
  - error_category: Error category
  - error_description: Human-readable description
  - message: Detailed error message
  - retry_action: Recommended retry action
  - details: Additional context
  - timestamp: When error occurred

## Analysis Required Flags

The system supports three analysis types that determine which APIs are called:

### 1. `image_only`
- **Description**: Only image analysis is performed
- **API Calls**: Image Analysis API only
- **Use Case**: When only visual content needs analysis
- **Output**: Image analysis results only

### 2. `text_only`
- **Description**: Only text analysis is performed
- **API Calls**: Text Analysis API only
- **Use Case**: When only textual content needs analysis
- **Output**: Text analysis results only

### 3. `image_and_text_both`
- **Description**: Both image and text analysis are performed
- **API Calls**: Both Image and Text Analysis APIs
- **Use Case**: Comprehensive content analysis
- **Output**: Combined analysis results

## Sample Payloads

### 1. Valid Response (merged_records1.json)
```json
{
  "processing_status": "SUCCESS",
  "analysis": [
    {
      "mode": "audit",
      "attribute": "title",
      "attribute_score": 0.001,
      "is_compliant": 1,
      "remarks": "Title content has changed"
    }
    // ... more analysis results
  ],
  "text_analysis_time": 0.010217905044555664,
  "image_analysis_time": 0.5114536285400391,
  "total_time_taken(text+image)": 0.5216715335845947,
  "score": 0,
  "kafka_partition": 2
}
```

### 2. Error Response with retry_after_correction (merged_records.json)
```json
{
  "processing_status": "FAILED",
  "error_details": {
    "error_code": "ERR_1008",
    "error_category": "VALIDATION_ERROR",
    "error_description": "Retry after correction only - Image download failed",
    "message": "Failed to download image from reference_data_thumbnail_key URL...",
    "retry_action": "retry_after_correction",
    "details": {
      "url": "https://1d2h987he92n6ry.cloudfront.net/images/...",
      "url_type": "reference_data_thumbnail_key",
      "location": "reference_data.images_v2"
    },
    "timestamp": "2025-08-06 08:01:30 UTC"
  }
}
```

### 3. Analysis Required Flag Examples

#### image_only.json
```json
{
  "analysis_required": ["image_only"],
  "analysis": [
    {
      "mode": "audit",
      "attribute": "images",
      "attribute_score": 100,
      "is_compliant": 1
    },
    {
      "mode": "audit",
      "attribute": "secondary_images",
      "attribute_score": 100,
      "is_compliant": 1
    }
  ]
}
```

#### text_only.json
```json
{
  "analysis_required": ["text_only"],
  "analysis": [
    {
      "mode": "audit",
      "attribute": "title",
      "attribute_score": 0.001,
      "is_compliant": 1
    },
    {
      "mode": "audit",
      "attribute": "description",
      "attribute_score": 0.001,
      "is_compliant": 1
    },
    {
      "mode": "audit",
      "attribute": "bullets",
      "attribute_score": 0.001,
      "is_compliant": 1
    }
  ]
}
```

#### image_and_text_both.json
```json
{
  "analysis_required": ["image_and_text_both"],
  "analysis": [
    {
      "mode": "audit",
      "attribute": "title",
      "attribute_score": 0.001,
      "is_compliant": 1
    },
    {
      "mode": "audit",
      "attribute": "images",
      "attribute_score": 100,
      "is_compliant": 1
    }
    // ... both text and image analysis results
  ]
}
```

## Implementation Details

### Error Handling Flow
1. **Record Processing**: Each record is processed individually
2. **Validation**: Input validation occurs first
3. **API Calls**: Based on analysis_required flag
4. **Error Detection**: Errors are caught and categorized
5. **Response Generation**: Success or error response is created

### Retry Logic Implementation
```python
# From error_handler.py
def _get_retry_action(self, error_code: ErrorCode) -> str:
    if error_code.value.startswith("ERR_1"):
        return RetryAction.RETRY_AFTER_CORRECTION.value
    elif error_code.value.startswith("ERR_2"):
        return RetryAction.RETRY.value
    elif error_code.value.startswith("ERR_3"):
        return RetryAction.NO_RETRY.value
    elif error_code.value.startswith("ERR_4"):
        return RetryAction.RETRY_AFTER_CORRECTION.value
    return RetryAction.NO_RETRY.value
```

### Analysis Type Processing
```python
# From content_analysis_orchestrator_2.py
if analysis_required == "image_only":
    image_task = self.send_request(self.IMAGE_ANALYSIS_API_URL, [input_record])
elif analysis_required == "text_only":
    text_task = self.send_request(self.TEXT_ANALYSIS_API_URL, [input_record])
elif analysis_required == "image_and_text_both":
    image_task = self.send_request(self.IMAGE_ANALYSIS_API_URL, [input_record])
    text_task = self.send_request(self.TEXT_ANALYSIS_API_URL, [input_record])
```

## Best Practices

### For Retry Logic
1. **Use appropriate retry actions** based on error type
2. **Implement exponential backoff** for retry actions
3. **Log all retry attempts** for monitoring
4. **Set maximum retry limits** to prevent infinite loops

### For Analysis Types
1. **Choose analysis type** based on business requirements
2. **Consider performance impact** of combined analysis
3. **Validate analysis_required** field format
4. **Handle missing analysis types** gracefully

### For Error Handling
1. **Categorize errors properly** using error codes
2. **Provide detailed error context** for debugging
3. **Include retry recommendations** in error responses
4. **Maintain error logs** for analysis and improvement

## Monitoring and Observability

The system provides comprehensive logging for:
- **Processing status** of each record
- **Error details** with categorization
- **Retry actions** taken
- **Performance metrics** (timing information)
- **Success/failure rates** for monitoring

This documentation provides a comprehensive overview of the retry system, payload structures, and analysis types used in the Content Analysis Orchestrator system.
