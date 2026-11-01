# Master Data, Migrations, and Seed Planning

Apply in every mode when requirements involve persisted master/reference data,
schema changes, initial data, or a user request for a seeder. Schema and seed needs
are separate decisions: existing tables can need seed data without a schema change.

- Determine whether the data belongs in an existing table, a new table, or an
  existing non-table representation, based on requirements and verified storage.
  Master data does not automatically require a new table. When a table is needed,
  define its columns/types, identity or stable codes, required/default values,
  uniqueness, relationships, and relevant indexes in `technical-design.md` §3.
- For a new or changed table, explicitly recommend versioned migration files in
  the project's migration directory and format. Identify proposed paths/names
  (follow the numbering convention; mark unallocated versions as proposed), forward
  changes, and rollback or a recovery path if reversal is unsafe. Include data
  backfills where needed. A model definition alone does not cover schema delivery.
- When the user requests a seeder, include a concrete seed-data recommendation,
  not only a seeder implementation task. Also recommend initial data when required
  for the feature to work; keep optional demo/test data distinct. Describe the
  target table, proposed records with stable keys and relevant values, purpose,
  source, and target environments. Use supplied requirements or authoritative
  project data; label illustrative suggestions as proposals. If required business
  values are unknown, identify the needed source/decision and block only dependent
  work rather than inventing an authoritative dataset.
- Follow the project's seeding mechanism; if absent, propose a suitable mechanism
  and location. Distinguish schema migrations, required reference-data migrations,
  backfills of existing rows, and repeatable seeders by purpose. If required data is
  delivered by a data migration, say so rather than planning duplicate insertion.
  Define rerun/conflict behavior using stable keys, dependency order, and rollback
  or cleanup behavior that accounts for existing references and user-managed data.
- Carry migration and seed artifacts into strategy inventory/reuse and explicit
  implementation tasks with dependencies and DoD. Order table creation before its
  data load and required data before consuming flows. Plan verification of schema
  constraints, required rows, seed reruns/conflicts, and applicable recovery behavior.
  Describe environment-specific execution in rollout. These are planning outputs;
  migration files and seeders are created during implementation.

