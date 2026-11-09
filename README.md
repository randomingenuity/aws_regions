# AWS regions catalog

This directory holds a community-maintained snapshot of AWS public region metadata so developers and engineering teams can use **human-readable names** in internal tools and **latitude/longitude** when placing regions on geographic maps (dashboards, capacity views, incident comms, and similar).

## Source data

The catalog was derived from the infrastructure map embedded on AWS’s marketing page:

[AWS Global Infrastructure — Regions and Availability Zones](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/)

That page publishes region display names, availability, launch year, and map coordinates. We normalized and enriched the export; it is **not** an official AWS API. For authoritative region codes and opt-in status, use [AWS documentation](https://docs.aws.amazon.com/global-infrastructure/latest/regions/aws-regions.html) or account APIs such as `DescribeRegions` / `ListRegions`.

## Files

| Artifact | Role |
| -------- | ---- |
| `continents.yaml` | Regions grouped under AWS marketing **continents** (same nesting as on the source page). |
| `regions.yaml` | All regions in one flat map keyed by region code (for quick lookup by `us-east-1`, etc.). |

## YAML structure

### `continents.yaml`

A **sequence** of continent records. Each record includes the continent-level fields from the source map (for example `name`, `lat`, `lng`) and a **`regions`** mapping whose keys are AWS region identifiers (`us-east-1`, `eu-west-1`, …). Each value is one region object (see below).

### `regions.yaml`

A **single mapping** from region identifier to region object. It is the merge of every `regions` block under all continents in `continents.yaml`, with the same fields per region.

### Region object

Keys are region **codes** only in the parent `regions` map in `continents.yaml` (or at the top level in `regions.yaml`); the block itself does not repeat the code. The two files share the same core fields; `regions.yaml` also adds **`continent_name`** when continents are merged into one map.

| Field | Meaning |
| ----- | ------- |
| `label` | Display string from AWS (e.g. `US East (Northern Virginia)`). |
| `continent_name` | *( `regions.yaml` only. )* Marketing continent that contains the region (e.g. `North America`). |
| `isolation_name` | Text before `(` in `label` (AWS geographic/product prefix). |
| `region_name` | Text inside parentheses in `label`, or the full `label` when there are no parentheses. |
| `lat`, `lng` | Approximate map coordinates from the source page. |
| `launched` | Launch year string when present in the source. |
| `available` | Whether the source marks the region as available (`true` / `false`). |

## Community maintenance

This catalog is provided through **community effort**, not AWS support. To fix naming, coordinates, or newly announced regions, open a **pull request** with an updated `continents.yaml`, then regenerate the flat catalog:

```bash
./render_regions
```

Install dependencies once, then regenerate after editing `continents.yaml`:

```bash
pip install -r requirements.txt
./render_regions
```

Describe what changed on the AWS page or in official docs so reviewers can verify the diff.
