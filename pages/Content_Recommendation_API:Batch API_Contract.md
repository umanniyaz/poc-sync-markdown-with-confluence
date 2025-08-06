# Content Recommendation API - Batch API Contract

## Overview

The Content Recommendation API provides batch processing capabilities for content auditing and optimization. This document outlines the complete API contract for all endpoints, including request/response formats, error handling, and usage examples.

---

## Table of Contents

1. [Content Audit Endpoint](#content-audit-endpoint)
2. [Content Optimization Endpoint](#content-optimization-endpoint)
3. [Job Management Endpoints](#job-management-endpoints)
4. [Health & Configuration Endpoints](#health--configuration-endpoints)
5. [Error Handling](#error-handling)
6. [Examples](#examples)

---

## Content Audit Endpoint

### POST /content-audit/

Submits a content audit job for background processing with the new payload structure.

#### Request Format

**Content-Type:** `application/json`

```json
{
  "records": [
    {
      "product_attributes": {
        "product_category": "string",
        "product_brand": "string", 
        "product_sub_category": "string"
      },
      "audit_attributes": [
        "title",
        "bullet", 
        "description",
        "specification"
      ],
      "guidelines": [
        {
          "guide_id": "string",
          "title": "string",
          "field": "string",
          "rules": "string",
          "severity": "string",
          "description": "string"
        }
      ],
      "meta_info": {
        "reference_product_id": "integer",
        "source": "string",
        "account_id": "string",
        "major_version": "string"
      },
      "actual_value": {
        "title": "string",
        "bullet": ["string"],
        "description": "string",
        "specification": {
          "key": "value"
        },
        "image_urls": ["string"]
      },
      "reference_value": [
        {
          "meta_info": {
            "reference_product_id": "integer",
            "source": "string",
            "account_id": "string",
            "major_version": "string"
          },
          "title": "string",
          "bullet": ["string"],
          "description": "string",
          "specification": {
            "key": "value"
          },
          "image_urls": ["string"]
        }
      ]
    }
  ]
}
```

#### Response Format

```json
{
  "job_id": "uuid",
  "status": "pending",
  "message": "string",
  "submitted_at": "2024-01-01T00:00:00Z"
}
```

#### Example Request

```json
{
  "records": [
    {
      "product_attributes": {
        "product_category": "Pliers",
        "product_brand": "Channellock",
        "product_sub_category": "-"
      },
      "audit_attributes": [
        "title",
        "bullet",
        "description", 
        "specification"
      ],
      "guidelines": [
        {
          "guide_id": "GUIDE_1",
          "title": "Supported Promotional Language",
          "field": "Description",
          "rules": "Avoid unsupported promotional language like \"guarantee\" or \"industry-leading\" unless substantiated with documentation",
          "severity": "Medium",
          "description": "Prevents unsubstantiated claims that could lead to customer disputes"
        }
      ],
      "meta_info": {
        "reference_product_id": 6799,
        "source": "Lowes-US",
        "account_id": "260509",
        "major_version": "v202508010000"
      },
      "actual_value": {
        "title": "CHANNELLOCK 10-Inches Tongue and groove pliers | 415",
        "bullet": [
          "Smooth jaw for use on plated fixtures and surfaces (chrome, nickel)",
          "Undercut Tongue and Groove design won't slip"
        ],
        "description": "All the versatility and strength of a straight jaw plier...",
        "specification": {
          "Color/Finish Family": "Blue",
          "Type": "Tongue and groove pliers",
          "Overall Length (Inches)": "10"
        },
        "image_urls": [
          "https://mobileimages.lowes.com/productimages/0ee303a8-84ab-4b53-908d-04efdcdf51c9/00248058.jpg"
        ]
      },
      "reference_value": [
        {
          "meta_info": {
            "reference_product_id": 6799,
            "source": "Walmart-US",
            "account_id": "260509",
            "major_version": "v202508010000"
          },
          "title": "CHANNELLOCK 415 10\" Smooth Jaw Straight Jaw Tongue & Groove Plier",
          "bullet": [],
          "description": "CHANNELLOCK® 415 10\" Tongue & Groove Pliers have all the versatility...",
          "specification": {
            "Features": "Straight Smooth-Jaw",
            "Brand": "Channellock",
            "Manufacturer Part Number": "415"
          },
          "image_urls": [
            "https://i5.walmartimages.com/seo/Channellock-415-10-in-Smooth-Jaw-Straight-Jaw-Tongue-Groove-Plier_7865669a-e6f4-4df7-aad5-671ba3038c60.73f2e394f821e39fad83ac0f26206be9.jpeg"
          ]
        }
      ]
    }
  ]
}
```

#### Example Response

```json
{
  "job_id": "550e8400-e29b-41d4-a716-446655440000",
  "status": "pending",
  "message": "Audit job submitted successfully. Processing 1 records with new structure.",
  "submitted_at": "2024-01-01T12:00:00Z"
}
```

---

## Content Optimization Endpoint

### POST /optimize-content/

Submits a content optimization job for background processing based on audit output.

#### Request Format

**Content-Type:** `application/json`

```json
{
  "records": [
    {
      "record_id": "integer",
      "pdp_tagged": {
        "title_attributes": {
          "content": {
            "attributes": {
              "brand": ["string"],
              "type": ["string"],
              "size": ["string"],
              "model": ["string"]
            }
          },
          "token_usage": {
            "input_tokens": "integer",
            "output_tokens": "integer"
          }
        },
        "title_keywords": {
          "content": {
            "keywords": {
              "short_tail": ["string"],
              "long_tail": ["string"],
              "related_terms": ["string"]
            }
          },
          "token_usage": {
            "input_tokens": "integer",
            "output_tokens": "integer"
          }
        }
      },
      "audit": {
        "title": {
          "compliance_issues": [],
          "competitor_gaps": [
            {
              "meta_info": {
                "reference_product_id": "integer",
                "source": "string",
                "account_id": "string",
                "major_version": "string"
              },
              "section": "string",
              "description": "string",
              "reason": "string"
            }
          ]
        },
        "bullet": {
          "compliance_issues": [],
          "competitor_gaps": []
        },
        "description": {
          "compliance_issues": [],
          "competitor_gaps": []
        },
        "specification": {
          "compliance_issues": [],
          "competitor_gaps": []
        }
      },
      "meta_info": {
        "reference_product_id": "integer",
        "source": "string",
        "account_id": "string",
        "major_version": "string"
      }
    }
  ],
  "optimization_options": {
    "optimization_types": [
      {
        "type": "string",
        "description": "string",
        "is_selected": "boolean"
      }
    ],
    "tone_options": [
      {
        "tone": "string",
        "description": "string",
        "is_selected": "boolean"
      }
    ],
    "additional_options": [
      {
        "option": "string",
        "description": "string",
        "is_selected": "boolean"
      }
    ]
  }
}
```

#### Response Format

```json
{
  "job_id": "uuid",
  "status": "pending",
  "message": "string",
  "submitted_at": "2024-01-01T00:00:00Z"
}
```

#### Example Request

```json
{
  "records": [
    {
      "record_id": 6799,
      "pdp_tagged": {
        "title_attributes": {
          "content": {
            "attributes": {
              "brand": ["CHANNELLOCK"],
              "type": ["Tongue and groove pliers"],
              "size": ["10-Inches"],
              "model": ["415"]
            }
          },
          "token_usage": {
            "input_tokens": 146,
            "output_tokens": 58
          }
        }
      },
      "audit": {
        "title": {
          "compliance_issues": [],
          "competitor_gaps": [
            {
              "meta_info": {
                "reference_product_id": 6799,
                "source": "Walmart-US",
                "account_id": "260509",
                "major_version": "v202508010000"
              },
              "section": "title",
              "description": "Missing 'Smooth Jaw' and 'Straight Jaw' attributes",
              "reason": "The competitor title includes 'Smooth Jaw Straight Jaw' which provides more specific information about the pliers' jaw type."
            }
          ]
        }
      },
      "meta_info": {
        "reference_product_id": 6799,
        "source": "Walmart-US",
        "account_id": "260509",
        "major_version": "v202508010000"
      }
    }
  ],
  "optimization_options": {
    "optimization_types": [
      {
        "type": "Standard",
        "description": "General optimization for engagement and SEO",
        "is_selected": true
      },
      {
        "type": "Complete",
        "description": "Comprehensive rewrite for maximum engagement and SEO",
        "is_selected": false
      }
    ],
    "tone_options": [
      {
        "tone": "Informative",
        "description": "Factual and educational tone",
        "is_selected": true
      },
      {
        "tone": "Promotional",
        "description": "Highlights offers and creates urgency",
        "is_selected": false
      }
    ],
    "additional_options": [
      {
        "option": "Include Compliance Issues",
        "description": "Address retailer guidelines and content quality issues",
        "is_selected": true
      },
      {
        "option": "Address Competitor Gaps",
        "description": "Optimize based on competitor content insights",
        "is_selected": true
      }
    ]
  }
}
```

#### Example Response

```json
{
  "job_id": "550e8400-e29b-41d4-a716-446655440001",
  "status": "pending",
  "message": "Optimization job submitted successfully. Processing 1 records.",
  "submitted_at": "2024-01-01T12:00:00Z"
}
```

---

## Job Management Endpoints

### GET /get-job-status/{job_id}

Retrieves the status and progress of a submitted job.

#### Path Parameters

- `job_id` (string, required): The unique identifier of the job

#### Response Format

```json
{
  "job_id": "uuid",
  "status": "pending|running|completed|failed|cancelled",
  "progress": {
    "processed": "integer",
    "total": "integer",
    "current_record_id": "string",
    "percentage": "number",
    "stage": "string",
    "records_remaining": "integer",
    "estimated_completion_timestamp": "number",
    "failed_records_count": "integer"
  },
  "error": "string",
  "submitted_at": "2024-01-01T00:00:00Z",
  "started_at": "2024-01-01T00:00:00Z",
  "completed_at": "2024-01-01T00:00:00Z",
  "estimated_completion": "2024-01-01T00:00:00Z"
}
```

#### Example Response

```json
{
  "job_id": "550e8400-e29b-41d4-a716-446655440000",
  "status": "running",
  "progress": {
    "processed": 5,
    "total": 10,
    "current_record_id": "6799",
    "percentage": 50.0,
    "stage": "processing_record_5",
    "records_remaining": 5,
    "estimated_completion_timestamp": 1704110400,
    "failed_records_count": 0
  },
  "error": null,
  "submitted_at": "2024-01-01T12:00:00Z",
  "started_at": "2024-01-01T12:00:01Z",
  "completed_at": null,
  "estimated_completion": "2024-01-01T12:00:30Z"
}
```

### GET /get-job-results/{job_id}

Retrieves the results of a completed job.

#### Path Parameters

- `job_id` (string, required): The unique identifier of the job

#### Response Format

```json
{
  "results": [
    {
      "record_id": "integer",
      "pdp_tagged": {
        "title_attributes": {
          "content": {
            "attributes": {
              "brand": ["string"],
              "type": ["string"],
              "size": ["string"],
              "model": ["string"]
            }
          },
          "token_usage": {
            "input_tokens": "integer",
            "output_tokens": "integer"
          }
        }
      },
      "audit": {
        "title": {
          "compliance_issues": [],
          "competitor_gaps": [
            {
              "meta_info": {
                "reference_product_id": "integer",
                "source": "string",
                "account_id": "string",
                "major_version": "string"
              },
              "section": "string",
              "description": "string",
              "reason": "string"
            }
          ]
        }
      }
    }
  ],
  "metrics": {
    "total_wall_clock_time_seconds": "number",
    "average_record_latency_seconds": "number",
    "total_record_processing_time_seconds": "number",
    "concurrency_efficiency": "number",
    "total_cost_usd": "number",
    "per_record": [
      {
        "record_id": "integer",
        "latency": "number",
        "sections": {
          "title": {
            "input_tokens": "integer",
            "output_tokens": "integer",
            "input_cost": "number",
            "output_cost": "number",
            "section_cost": "number"
          }
        },
        "record_cost": "number"
      }
    ],
    "successful_records": "integer",
    "failed_records": "integer",
    "success_rate": "number",
    "threading_info": {
      "max_workers_used": "integer",
      "records_processed": "integer",
      "concurrent_processing": "boolean"
    }
  }
}
```

#### Example Response (Audit Results)

```json
{
  "results": [
    {
      "record_id": 6799,
      "pdp_tagged": {
        "title_attributes": {
          "content": {
            "attributes": {
              "brand": ["CHANNELLOCK"],
              "type": ["Tongue and groove pliers"],
              "size": ["10-Inches"],
              "model": ["415"]
            }
          },
          "token_usage": {
            "input_tokens": 146,
            "output_tokens": 58
          }
        }
      },
      "audit": {
        "title": {
          "compliance_issues": [],
          "competitor_gaps": [
            {
              "meta_info": {
                "reference_product_id": 6799,
                "source": "Walmart-US",
                "account_id": "260509",
                "major_version": "v202508010000"
              },
              "section": "title",
              "description": "Missing 'Smooth Jaw' and 'Straight Jaw' attributes",
              "reason": "The competitor title includes 'Smooth Jaw Straight Jaw' which provides more specific information about the pliers' jaw type."
            }
          ]
        }
      }
    }
  ],
  "metrics": {
    "total_wall_clock_time_seconds": 45.2,
    "average_record_latency_seconds": 8.5,
    "total_record_processing_time_seconds": 85.0,
    "concurrency_efficiency": 1.88,
    "total_cost_usd": 0.0234,
    "successful_records": 1,
    "failed_records": 0,
    "success_rate": 100.0,
    "threading_info": {
      "max_workers_used": 6,
      "records_processed": 1,
      "concurrent_processing": true
    }
  }
}
```

#### Example Response (Optimization Results)

```json
{
  "results": [
    {
      "record_id": 6799,
      "pdp_tagged": {
        "title_attributes": {
          "content": {
            "attributes": {
              "brand": ["CHANNELLOCK"],
              "type": ["Tongue and groove pliers"],
              "size": ["10-Inches"],
              "model": ["415"]
            }
          }
        }
      },
      "audit": {
        "title": {
          "compliance_issues": [],
          "competitor_gaps": [
            {
              "meta_info": {
                "reference_product_id": 6799,
                "source": "Walmart-US",
                "account_id": "260509",
                "major_version": "v202508010000"
              },
              "section": "title",
              "description": "Missing 'Smooth Jaw' and 'Straight Jaw' attributes",
              "reason": "The competitor title includes 'Smooth Jaw Straight Jaw' which provides more specific information about the pliers' jaw type."
            }
          ]
        }
      },
      "optimization": {
        "meta_info": {
          "reference_product_id": 6799,
          "source": "Walmart-US",
          "account_id": "260509",
          "major_version": "v202508010000"
        },
        "optimized_content": {
          "title": {
            "optimized_text": "CHANNELLOCK 10\" Smooth Jaw Straight Jaw Tongue and Groove Pliers, Model 415",
            "optimization_reasoning": "Incorporated brand name, specific size, jaw type, and model number for improved clarity and searchability.",
            "keywords_incorporated": [
              "CHANNELLOCK",
              "Tongue and groove pliers",
              "smooth jaw",
              "straight jaw"
            ],
            "attributes_used": [
              "brand",
              "size",
              "type",
              "model",
              "jaw_type"
            ],
            "improvements_made": [
              "Added jaw type information",
              "Used standard measurement format (10\")",
              "Included model number"
            ]
          },
          "bullet": {
            "optimized_text": [
              "SMOOTH JAW DESIGN: Ideal for plated fixtures, chrome, and nickel surfaces without scratching",
              "PERMALOCK FASTENER: Eliminates nut and bolt failure for enhanced durability"
            ],
            "optimization_reasoning": "Restructured bullet points to highlight key features, benefits, and usage scenarios while maintaining an informative tone.",
            "keywords_incorporated": [
              "smooth jaw",
              "PERMALOCK",
              "high-carbon steel"
            ],
            "attributes_used": [
              "jaw_type",
              "suitable_for",
              "fastener",
              "material"
            ],
            "improvements_made": [
              "Emphasized smooth jaw design benefits",
              "Highlighted durability features",
              "Added usage scenarios"
            ]
          }
        },
        "optimization_summary": {
          "strategy_applied": "Standard optimization for engagement and SEO",
          "tone_applied": "Informative",
          "compliance_issues_resolved": [
            "No compliance issues were identified in the audit"
          ],
          "competitor_gaps_addressed": [
            "Added 'Smooth Jaw' and 'Straight Jaw' attributes to the title",
            "Used standard measurement format (10\") in the title",
            "Incorporated brand name CHANNELLOCK® in the description"
          ],
          "overall_improvements": [
            "Enhanced title with more specific product information",
            "Restructured bullet points for better feature highlighting",
            "Expanded description to include key features and benefits",
            "Improved SEO by incorporating relevant keywords throughout the content"
          ]
        }
      }
    }
  ],
  "metrics": {
    "total_wall_clock_time_seconds": 32.1,
    "average_record_latency_seconds": 6.2,
    "total_record_processing_time_seconds": 62.0,
    "concurrency_efficiency": 1.93,
    "total_cost_usd": 0.0187,
    "successful_records": 1,
    "failed_records": 0,
    "success_rate": 100.0,
    "threading_info": {
      "max_workers_used": 6,
      "records_processed": 1,
      "concurrent_processing": true
    }
  }
}
```

### DELETE /terminate-job/{job_id}

Terminates a running or pending job.

#### Path Parameters

- `job_id` (string, required): The unique identifier of the job

#### Response Format

```json
{
  "job_id": "uuid",
  "status": "cancelled",
  "message": "string"
}
```

#### Example Response

```json
{
  "job_id": "550e8400-e29b-41d4-a716-446655440000",
  "status": "cancelled",
  "message": "Job termination requested. The task will stop at the next checkpoint."
}
```

---

## Health & Configuration Endpoints

### GET /health

Retrieves the health status of the API.

#### Response Format

```json
{
  "status": "healthy",
  "active_jobs": "integer",
  "total_jobs": "integer",
  "endpoints": {
    "content_audit_new": "/content-audit-new/",
    "job_status": "/get-job-status/{job_id}",
    "job_results": "/get-job-results/{job_id}",
    "terminate_job": "/terminate-job/{job_id}",
    "optimize_content": "/optimize-content/"
  },
  "thread_pool_info": {
    "default_max_workers": "integer",
    "max_workers_range": "string",
    "concurrent_processing": "string"
  }
}
```

#### Example Response

```json
{
  "status": "healthy",
  "active_jobs": 2,
  "total_jobs": 15,
  "endpoints": {
    "content_audit_new": "/content-audit-new/",
    "job_status": "/get-job-status/{job_id}",
    "job_results": "/get-job-results/{job_id}",
    "terminate_job": "/terminate-job/{job_id}",
    "optimize_content": "/optimize-content/"
  },
  "thread_pool_info": {
    "default_max_workers": 8,
    "max_workers_range": "1-20",
    "concurrent_processing": "enabled"
  }
}
```

### GET /config

Retrieves the current API configuration.

#### Response Format

```json
{
  "multithreading": {
    "enabled": "boolean",
    "default_max_workers": "integer",
    "max_workers_range": "string",
    "description": "string"
  },
  "bedrock": {
    "concurrent_calls": "string",
    "description": "string"
  },
  "cost_tracking": {
    "enabled": "boolean",
    "sections": ["string"],
    "description": "string"
  },
  "new_audit_structure": {
    "enabled": "boolean",
    "description": "string",
    "fields": {
      "product_attributes": "string",
      "audit_attributes": "string",
      "guidelines": "string",
      "meta_info": "string",
      "actual_value": "string",
      "reference_value": "string"
    }
  },
  "optimization": {
    "enabled": "boolean",
    "description": "string",
    "fields": {
      "optimization_types": "string",
      "tone_options": "string",
      "additional_options": "string"
    }
  }
}
```

#### Example Response

```json
{
  "multithreading": {
    "enabled": true,
    "default_max_workers": 8,
    "max_workers_range": "1-20",
    "description": "Concurrent processing of records using ThreadPoolExecutor"
  },
  "bedrock": {
    "concurrent_calls": "enabled",
    "description": "Each thread makes independent calls to AWS Bedrock"
  },
  "cost_tracking": {
    "enabled": true,
    "sections": [
      "title",
      "bullet",
      "description",
      "specification",
      "image",
      "optimization"
    ],
    "description": "Token usage and cost tracking per section"
  },
  "new_audit_structure": {
    "enabled": true,
    "description": "Updated audit structure with product_attributes, audit_attributes, guidelines, meta_info, actual_value, reference_value",
    "fields": {
      "product_attributes": "Contains product category, brand, sub-category",
      "audit_attributes": "List of sections to audit",
      "guidelines": "List of guideline objects with guide_id, title, field, rules, severity, description",
      "meta_info": "Contains reference_product_id, source, account_id, major_version",
      "actual_value": "Contains the actual content for each section",
      "reference_value": "List of competitor/reference data with meta_info"
    }
  },
  "optimization": {
    "enabled": true,
    "description": "Content optimization based on audit output and optimization rules",
    "fields": {
      "optimization_types": "Types of optimizations to apply (e.g., grammar, tone, clarity)",
      "tone_options": "Options for tone (e.g., formal, informal, neutral)",
      "additional_options": "Additional options for specific optimizations"
    }
  }
}
```

---

## Error Handling

### HTTP Status Codes

| Status Code | Description |
|-------------|-------------|
| 200 | Success |
| 400 | Bad Request - Invalid input data |
| 404 | Not Found - Job not found |
| 500 | Internal Server Error |

### Error Response Format

```json
{
  "detail": "string",
  "error_code": "string",
  "timestamp": "2024-01-01T00:00:00Z"
}
```

### Common Error Scenarios

#### 400 Bad Request - Invalid Input

```json
{
  "detail": "No records provided for audit",
  "error_code": "INVALID_INPUT",
  "timestamp": "2024-01-01T12:00:00Z"
}
```

#### 404 Not Found - Job Not Found

```json
{
  "detail": "Job 550e8400-e29b-41d4-a716-446655440000 not found",
  "error_code": "JOB_NOT_FOUND",
  "timestamp": "2024-01-01T12:00:00Z"
}
```

#### 400 Bad Request - Job Not Completed

```json
{
  "detail": "Job 550e8400-e29b-41d4-a716-446655440000 is not completed. Current status: running",
  "error_code": "JOB_NOT_COMPLETED",
  "timestamp": "2024-01-01T12:00:00Z"
}
```

---

## Examples

### Complete Workflow Example

#### Step 1: Submit Content Audit Job

```bash
curl -X POST "https://api.content-recommendation.com/v2/content-audit/" \
  -H "Content-Type: application/json" \
  -d @audit_final_input_payload.json
```

**Response:**
```json
{
  "job_id": "550e8400-e29b-41d4-a716-446655440000",
  "status": "pending",
  "message": "Audit job submitted successfully. Processing 1 records with new structure.",
  "submitted_at": "2024-01-01T12:00:00Z"
}
```

#### Step 2: Check Job Status

```bash
curl -X GET "https://api.content-recommendation.com/v2/get-job-status/550e8400-e29b-41d4-a716-446655440000"
```

**Response:**
```json
{
  "job_id": "550e8400-e29b-41d4-a716-446655440000",
  "status": "completed",
  "progress": {
    "processed": 1,
    "total": 1,
    "current_record_id": null,
    "percentage": 100.0,
    "stage": "completed",
    "records_remaining": 0,
    "failed_records_count": 0
  },
  "submitted_at": "2024-01-01T12:00:00Z",
  "started_at": "2024-01-01T12:00:01Z",
  "completed_at": "2024-01-01T12:00:45Z"
}
```

#### Step 3: Get Audit Results

```bash
curl -X GET "https://api.content-recommendation.com/v2/get-job-results/550e8400-e29b-41d4-a716-446655440000"
```

**Response:** See `audit_final_output_payload.json` format

#### Step 4: Submit Content Optimization Job

```bash
curl -X POST "https://api.content-recommendation.com/v2/optimize-content/" \
  -H "Content-Type: application/json" \
  -d @optimize_final_input_payload.json
```

**Response:**
```json
{
  "job_id": "550e8400-e29b-41d4-a716-446655440001",
  "status": "pending",
  "message": "Optimization job submitted successfully. Processing 1 records.",
  "submitted_at": "2024-01-01T12:01:00Z"
}
```

#### Step 5: Get Optimization Results

```bash
curl -X GET "https://api.content-recommendation.com/v2/get-job-results/550e8400-e29b-41d4-a716-446655440001"
```

**Response:** See `optimize_final_output_payload.json` format

---

## Data Types

### Job Status Enum

- `pending` - Job is queued for processing
- `running` - Job is currently being processed
- `completed` - Job has completed successfully
- `failed` - Job failed during processing
- `cancelled` - Job was cancelled by user

### Optimization Types

- `Standard` - General optimization for engagement and SEO
- `Complete` - Comprehensive rewrite for maximum engagement and SEO
- `SEO-Focused` - Optimized specifically for search engines
- `Sales-Focused` - Content rewritten with persuasive sales language
- `Technical Details Focus` - Content enhanced with detailed technical information

### Tone Options

- `Informative` - Factual and educational tone
- `Promotional` - Highlights offers and creates urgency
- `Enthusiastic` - Energetic and passionate tone
- `Technical` - Focuses on specifications and performance metrics
- `Casual` - Conversational and approachable
- `Premium/Luxury` - Emphasizes exclusivity and quality
- `Urgent/Limited Time` - Creates a sense of limited availability
- `Seasonal/Festive` - Connects the product to holidays or seasons

### Additional Options

- `Include Compliance Issues` - Address retailer guidelines and content quality issues
- `Address Competitor Gaps` - Optimize based on competitor content insights

---

## Best Practices

1. **Batch Size:** Keep batch sizes reasonable (1-100 records) for optimal performance
2. **Polling:** Use the job status endpoint to monitor progress instead of continuous polling
3. **Error Handling:** Always check for errors in the response and handle them appropriately
4. **Data Validation:** Validate input data before sending to the API
5. **Monitoring:** Use the health endpoint to monitor API status
6. **Cost Tracking:** Monitor the cost metrics in job results to optimize usage

---

## Support

For technical support or questions about this API:

- **Email:** uman.n@dataweave.com


---

*Last Updated: August 2024*
*API Version: 1.0.0* 