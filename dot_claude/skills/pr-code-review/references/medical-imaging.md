# Medical Imaging Domain Reference

Specialized review considerations for Radiology and Medical Imaging code.

## DICOM Standard Compliance

### Key Concepts

- **DICOM (Digital Imaging and Communications in Medicine)**: International standard for
  medical images and related information
- **SOP Class**: Service-Object Pair defining what operations can be performed on which objects
- **Transfer Syntax**: Encoding rules for DICOM data (implicit/explicit VR, compression)
- **IOD (Information Object Definition)**: Template for DICOM objects

### Review Checklist

- [ ] Correct handling of DICOM Value Representations (VR)
- [ ] Proper parsing of nested sequences
- [ ] Transfer syntax negotiation in network operations
- [ ] Handling of private tags and extended character sets
- [ ] Correct UID generation and uniqueness
- [ ] Proper handling of multi-frame images

### Common Issues

| Issue | Impact | Resolution |
|-------|--------|------------|
| Incorrect VR handling | Data corruption | Validate VR against data dictionary |
| Missing transfer syntax | Interoperability failures | Implement negotiation |
| Invalid UIDs | Duplicate studies | Use proper UID generation |
| Sequence depth limits | Parsing failures | Handle arbitrary nesting |

## Patient Data Privacy (PHI Handling)

### HIPAA Requirements

- **Minimum Necessary**: Access only required PHI
- **De-identification**: Remove or mask 18 HIPAA identifiers
- **Audit Logging**: Track all PHI access

### PHI Elements in DICOM

| Tag | Name | Action |
|-----|------|--------|
| (0010,0010) | Patient Name | De-identify |
| (0010,0020) | Patient ID | De-identify |
| (0010,0030) | Patient Birth Date | Remove or generalize |
| (0010,0040) | Patient Sex | May retain |
| (0008,0050) | Accession Number | De-identify |
| (0008,0080) | Institution Name | De-identify |

### Review Checklist

- [ ] No PHI in logs or error messages
- [ ] PHI encrypted at rest and in transit
- [ ] De-identification before export/research use
- [ ] Audit trail for PHI access
- [ ] Role-based access controls

## Integration Patterns

### PACS Integration

- **C-STORE**: Send images to PACS
- **C-FIND**: Query for studies/series/images
- **C-MOVE**: Retrieve images
- **C-GET**: Alternative retrieval method

### RIS/HIS Integration

- **HL7 ADT**: Patient demographics
- **HL7 ORM/OMI**: Orders
- **HL7 ORU**: Results/reports

### FHIR Resources

- **ImagingStudy**: References to DICOM studies
- **DiagnosticReport**: Radiology reports
- **Observation**: Findings and measurements

## Medical Device Software Regulations

### IEC 62304 Classification

| Class | Risk | Documentation Required |
|-------|------|------------------------|
| A | No injury possible | Basic |
| B | Non-serious injury | Moderate |
| C | Death or serious injury | Comprehensive |

### Review Considerations

- [ ] Risk classification documented
- [ ] Traceability to requirements
- [ ] Unit test coverage appropriate for class
- [ ] Anomaly handling documented
- [ ] Software architecture documented

## Image Processing Accuracy

### Validation Requirements

- [ ] Pixel data integrity after transformations
- [ ] Correct handling of Photometric Interpretation
- [ ] Window/Level calculations accurate
- [ ] Spatial calibration preserved
- [ ] Measurement accuracy validated

### Common Pitfalls

- Integer overflow in pixel calculations
- Loss of precision in floating-point operations
- Incorrect byte ordering (little/big endian)
- Missing handling of signed vs unsigned pixels
- Incorrect rescale slope/intercept application

## Audit Trail Requirements

### Required Events

- PHI access (view, export, print)
- User authentication events
- Configuration changes
- Data modifications
- Security events

### Log Format Requirements

- Timestamp (UTC)
- User identity
- Action performed
- Object accessed
- Outcome (success/failure)
- Source IP/workstation
